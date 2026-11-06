# terraform-aws-bymorning

A Terraform module that deploys [ByMorning](https://github.com/bymorning/bymorning), an open source, self-hosted MCP and model gateway, into a VPC
you already have, in commercial AWS or AWS GovCloud (US). It runs one ECS Fargate task (migrations, then the Gateway)
behind an Application Load Balancer, with RDS PostgreSQL 17, an S3 bucket, one KMS key, and Secrets Manager.

This repository is a mirror of `installers/aws/modules/bymorning` in the ByMorning repository, exported for the Terraform
Registry. Report issues and send changes there. ByMorning is provided as-is and community maintained, under the MIT
License. This module passed offline tests and one end-to-end test in a commercial AWS account; Bedrock model calls and
AWS GovCloud were not tested. Run the sandbox checklist in the ByMorning repository before you rely on it.

```hcl
module "bymorning" {
  source  = "bymorning/bymorning/aws"
  version = "1.3.3"

  image           = "ghcr.io/bymorning/bymorning@sha256:<digest>" # pinned by digest; verify it with cosign first
  hostname        = "bymorning.example.com"
  certificate_arn = "arn:aws:acm:us-east-1:123456789012:certificate/<id>"

  vpc_id                   = "vpc-0123456789abcdef0"
  service_subnet_ids       = ["subnet-aaa", "subnet-bbb"]
  load_balancer_subnet_ids = ["subnet-aaa", "subnet-bbb"]
  allowed_cidrs            = ["10.0.0.0/8"]
}
```

The Region selects the partition, and GovCloud always uses FIPS endpoints. See
[the AWS installer guide](https://github.com/bymorning/bymorning/blob/v1.3.3/installers/aws/README.md) for prerequisites,
verifying and mirroring the image, first sign-in, upgrades, and removal, and [examples/basic](examples/basic) for a root
module.
