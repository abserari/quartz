结合 devcloud 和之前的 minio 中间件思考一下问题：如何集成使用？


首先描述 [Minio Provier](https://registry.terraform.io/providers/refaktory/minio/latest/docs)， [[MInio]] 是需要自己部署的，[[Terraform]] minio 这个 [[terraform provider|provicer]]只是提供了配置选项。不过我们使用基础设施其实也就是配置了。没有启动功能用什么启动呢？
1. 自己本地启动
2. 通过 terraform 的其他云 provider 在云上申请一个这样的资源，比如 [AWS Terraform Provider](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)

当然，由于 [[terraform]] 的 [[terraform provider]] 编写比较简单，其实也可以自己编辑一个以 [[docker]] 作为 [[infrastructure]] 的包含 minio 启动流程的 provider 啦。比如组合 docker provider 声明一个  minio 镜像的 [[container]] 就好啦。
