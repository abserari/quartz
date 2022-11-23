---
title: "StartTestServer"
date: "2022-10-28"
---

```go
// StartTestServer runs a kube-apiserver, optionally calling out to the setup.ModifyServerRunOptions and setup.ModifyServerConfig functions

func StartTestServer(t testing.TB, setup TestServerSetup) (client.Interface, *rest.Config, TearDownFunc) {

certDir, err := os.MkdirTemp("", "test-integration-"+strings.ReplaceAll(t.Name(), "/", "_"))

if err != nil {

t.Fatalf("Couldn't create temp dir: %v", err)

}

stopCh := make(chan struct{})

var errCh chan error

tearDownFn := func() {

// Closing stopCh is stopping apiserver and cleaning up

// after itself, including shutting down its storage layer.

close(stopCh)

// If the apiserver was started, let's wait for it to

// shutdown clearly.

if errCh != nil {

err, ok := <-errCh

if ok && err != nil {

t.Error(err)

}

}

if err := os.RemoveAll(certDir); err != nil {

t.Log(err)

}

}

_, defaultServiceClusterIPRange, _ := netutils.ParseCIDRSloppy("10.0.0.0/24")

proxySigningKey, err := utils.NewPrivateKey()

if err != nil {

t.Fatal(err)

}

proxySigningCert, err := cert.NewSelfSignedCACert(cert.Config{CommonName: "front-proxy-ca"}, proxySigningKey)

if err != nil {

t.Fatal(err)

}

proxyCACertFile, _ := os.CreateTemp(certDir, "proxy-ca.crt")

if err := os.WriteFile(proxyCACertFile.Name(), utils.EncodeCertPEM(proxySigningCert), 0644); err != nil {

t.Fatal(err)

}

clientSigningKey, err := utils.NewPrivateKey()

if err != nil {

t.Fatal(err)

}

clientSigningCert, err := cert.NewSelfSignedCACert(cert.Config{CommonName: "client-ca"}, clientSigningKey)

if err != nil {

t.Fatal(err)

}

clientCACertFile, _ := os.CreateTemp(certDir, "client-ca.crt")

if err := os.WriteFile(clientCACertFile.Name(), utils.EncodeCertPEM(clientSigningCert), 0644); err != nil {

t.Fatal(err)

}

listener, _, err := genericapiserveroptions.CreateListener("tcp", "127.0.0.1:0", net.ListenConfig{})

if err != nil {

t.Fatal(err)

}

saSigningKeyFile, err := os.CreateTemp("/tmp", "insecure_test_key")

if err != nil {

t.Fatalf("create temp file failed: %v", err)

}

defer os.RemoveAll(saSigningKeyFile.Name())

if err = os.WriteFile(saSigningKeyFile.Name(), []byte(ecdsaPrivateKey), 0666); err != nil {

t.Fatalf("write file %s failed: %v", saSigningKeyFile.Name(), err)

}

kubeAPIServerOptions := options.NewServerRunOptions()

kubeAPIServerOptions.SecureServing.Listener = listener

kubeAPIServerOptions.SecureServing.BindAddress = netutils.ParseIPSloppy("127.0.0.1")

kubeAPIServerOptions.SecureServing.ServerCert.CertDirectory = certDir

kubeAPIServerOptions.ServiceAccountSigningKeyFile = saSigningKeyFile.Name()

kubeAPIServerOptions.Etcd.StorageConfig.Prefix = path.Join("/", uuid.New().String(), "registry")

kubeAPIServerOptions.Etcd.StorageConfig.Transport.ServerList = []string{GetEtcdURL()}

kubeAPIServerOptions.ServiceClusterIPRanges = defaultServiceClusterIPRange.String()

kubeAPIServerOptions.Authentication.RequestHeader.UsernameHeaders = []string{"X-Remote-User"}

kubeAPIServerOptions.Authentication.RequestHeader.GroupHeaders = []string{"X-Remote-Group"}

kubeAPIServerOptions.Authentication.RequestHeader.ExtraHeaderPrefixes = []string{"X-Remote-Extra-"}

kubeAPIServerOptions.Authentication.RequestHeader.AllowedNames = []string{"kube-aggregator"}

kubeAPIServerOptions.Authentication.RequestHeader.ClientCAFile = proxyCACertFile.Name()

kubeAPIServerOptions.Authentication.APIAudiences = []string{"https://foo.bar.example.com"}

kubeAPIServerOptions.Authentication.ServiceAccounts.Issuers = []string{"https://foo.bar.example.com"}

kubeAPIServerOptions.Authentication.ServiceAccounts.KeyFiles = []string{saSigningKeyFile.Name()}

kubeAPIServerOptions.Authentication.ClientCert.ClientCA = clientCACertFile.Name()

kubeAPIServerOptions.Authorization.Modes = []string{"Node", "RBAC"}

if setup.ModifyServerRunOptions != nil {

setup.ModifyServerRunOptions(kubeAPIServerOptions)

}

completedOptions, err := app.Complete(kubeAPIServerOptions)

if err != nil {

t.Fatal(err)

}

if errs := completedOptions.Validate(); len(errs) != 0 {

t.Fatalf("failed to validate ServerRunOptions: %v", utilerrors.NewAggregate(errs))

}

kubeAPIServerConfig, _, _, err := app.CreateKubeAPIServerConfig(completedOptions)

if err != nil {

t.Fatal(err)

}

if setup.ModifyServerConfig != nil {

setup.ModifyServerConfig(kubeAPIServerConfig)

}

kubeAPIServer, err := app.CreateKubeAPIServer(kubeAPIServerConfig, genericapiserver.NewEmptyDelegate())

if err != nil {

t.Fatal(err)

}

errCh = make(chan error)

go func() {

defer close(errCh)

if err := kubeAPIServer.GenericAPIServer.PrepareRun().Run(stopCh); err != nil {

errCh <- err

}

}()

// Adjust the loopback config for external use (external server name and CA)

kubeAPIServerClientConfig := rest.CopyConfig(kubeAPIServerConfig.GenericConfig.LoopbackClientConfig)

kubeAPIServerClientConfig.CAFile = path.Join(certDir, "apiserver.crt")

kubeAPIServerClientConfig.CAData = nil

kubeAPIServerClientConfig.ServerName = ""

// wait for health

err = wait.PollImmediate(100*time.Millisecond, 10*time.Second, func() (done bool, err error) {

select {

case err := <-errCh:

return false, err

default:

}

healthzConfig := rest.CopyConfig(kubeAPIServerClientConfig)

healthzConfig.ContentType = ""

healthzConfig.AcceptContentTypes = ""

kubeClient, err := client.NewForConfig(healthzConfig)

if err != nil {

// this happens because we race the API server start

t.Log(err)

return false, nil

}

healthStatus := 0

kubeClient.Discovery().RESTClient().Get().AbsPath("/healthz").Do(context.TODO()).StatusCode(&healthStatus)

if healthStatus != http.StatusOK {

return false, nil

}

if _, err := kubeClient.CoreV1().Namespaces().Get(context.TODO(), "default", metav1.GetOptions{}); err != nil {

return false, nil

}

if _, err := kubeClient.CoreV1().Namespaces().Get(context.TODO(), "kube-system", metav1.GetOptions{}); err != nil {

return false, nil

}

return true, nil

})

if err != nil {

t.Fatal(err)

}

kubeAPIServerClient, err := client.NewForConfig(kubeAPIServerClientConfig)

if err != nil {

t.Fatal(err)

}

return kubeAPIServerClient, kubeAPIServerClientConfig, tearDownFn

}
```