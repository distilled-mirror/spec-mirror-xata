> ## Documentation Index
> Fetch the complete documentation index at: https://xata.io/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# AWS PrivateLink

> Connect to your Xata Postgres databases privately from your AWS VPC, without traffic leaving the AWS network

AWS PrivateLink lets you reach your Xata databases from your own AWS VPC over
the AWS private network. Traffic never traverses the public internet, and your
connection strings stay exactly the same.

PrivateLink is available on request. Please contact us via our regular support channels and
provide us with:

* The **AWS region** of your Xata databases (e.g. `us-east-1`)
* The **AWS account ID** (or IAM role/user ARN) you will create the endpoint
  from.

In return we will provide you with the **service name** of the Xata
VPC endpoint service to connect to. It will be in the form
`com.amazonaws.vpce.us-east-1.vpce-svc-...`.

## How it works

Xata exposes its Postgres gateway as a VPC endpoint service. You create an
**interface VPC endpoint** against it in your VPC, which places network
interfaces (ENIs) with private IPs in your subnets. With private DNS enabled,
your existing database hostnames (`<branch>.us-east-1.xata.tech`) resolve to
those private IPs inside your VPC. This means the same connection string uses the
private path from your VPC and the public path from everywhere else.

## Prerequisites

* A VPC in the same region and Availability Zone (AZ) as your Xata databases.
* The VPC must have **DNS support** and **DNS hostnames** enabled
  (`enableDnsSupport` and `enableDnsHostnames`, both on by default for most
  VPCs).

## Step 1 — Create a security group for the endpoint

The endpoint's network interfaces need a security group that allows inbound
traffic from your clients:

* TCP `5432` (Postgres wire protocol) from your application's security group
  or CIDR ranges
* TCP `443` (HTTPS) from the same sources — only needed if you use the
  serverless HTTP driver

## Step 2 — Create the interface VPC endpoint

In the AWS console, go to **VPC → PrivateLink and Lattice → Endpoints** and
click **Create endpoint**:

1. Optionally give the endpoint a **Name tag** (e.g. `xata`).
2. Under **Type**, select **Endpoint services that use NLBs and GWLBs**
   ("Find services shared with you by service name").
3. Leave **Enable Cross Region endpoint** unchecked. Your endpoint must be
   in the same region as your Xata databases.
4. In the **Service name** field, paste the service name provided by Xata
   (`com.amazonaws.vpce.us-east-1.vpce-svc-…`) and click **Verify service**.
   If this fails with a permissions error, please contact us.
5. The form expands: select your VPC and one subnet per Availability Zone
   you want to use.
6. Attach the security group from step 1.
7. Enable **Private DNS names**. This is what makes your existing Xata
   hostnames resolve to the endpoint inside your VPC. No DNS changes should be
   needed on your side. You can also enable it later from the endpoint's
   **Actions → Modify private DNS name**.
8. Click **Create endpoint**.

The Terraform equivalent:

```hcl theme={null}
resource "aws_vpc_endpoint" "xata" {
  vpc_id              = var.vpc_id
  service_name        = "<service name provided by Xata>"
  vpc_endpoint_type   = "Interface"
  subnet_ids          = var.private_subnet_ids
  security_group_ids  = [aws_security_group.xata_endpoint.id]
  private_dns_enabled = true
}
```

## Step 3 — Connect

Connect to the database endpoints as shown in the Xata console. For example:

```bash theme={null}
psql "postgresql://<user>:<password>@<branch>.us-east-1.xata.tech:5432/<database>?sslmode=verify-full"
```

Inside your VPC, the hostname now resolves to your endpoint's private IPs and
traffic flows over PrivateLink. From anywhere else, the same hostname resolves
to the public endpoint as usual. You can verify the private path from an
instance in your VPC:

```bash theme={null}
dig +short <branch>.us-east-1.xata.tech
# should return private IPs from your VPC's subnets (the endpoint ENIs)
```
