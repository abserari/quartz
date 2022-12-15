---
created: ["2022-10-12 17:41"]
aliases: ["Blog Post Code"]
tags:
- Blog/
---

### 1. 创建配置文件

`.terraformrc`是 [[Terraform]] CLI的配置文件

```
plugin_cache_dir  = "/root/.terraform.d/terraform-plugin-cache" 
disable_checkpoint = true
provider_installation {
  filesystem_mirror {
    path    = "/root/.terraform.d/terraform-plugin-cache"
    include = ["registry.terraform.io/*/*"]
  }
}
```

-   plugin_cache_dir 是插件的缓存目录（此目录需要提前创建不然init报错）
-   disable_checkpoint 禁用 需要连接HashiCorp 提供的网络服务的升级和安全公告检查


### 2. 进行初始化

插件下载方式有两种：

1.  通过 `terraform init` 自动下载 provider 插件；
2.  登入`registry.terraform.io`手动到 `GitHub`下载，并按照目录结构存放到`plugin_cache_dir`;

```bash
❯ tree /root/.terraform.d
/root/.terraform.d
├── checkpoint_signature
└── terraform-plugin-cache
    └── registry.terraform.io
        ├── coder
        │   └── coder
        │       └── 0.5.0
        │           └── linux_amd64
        │               └── terraform-provider-coder_v0.5.0
        └── kreuzwerker
            └── docker
                └── 2.20.2
                    └── linux_amd64
                        └── terraform-provider-docker_v2.20.2
```

然后运行 `terraform init` 即可
