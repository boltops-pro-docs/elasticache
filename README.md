<!-- note marker start -->
**NOTE**: This repo contains only the documentation for the private BoltsOps Pro repo code.
Original file: https://github.com/boltopspro/elasticache/blob/master/README.md
The docs are publish so they are available for interested customers.
For access to the source code, you must be a paying BoltOps Pro subscriber.
If are interested, you can contact us at contact@boltops.com or https://www.boltops.com

<!-- note marker end -->

# ElastiCache CloudFormation Blueprint: Memcached and Redis

![CodeBuild](https://codebuild.us-west-2.amazonaws.com/badges?uuid=eyJlbmNyeXB0ZWREYXRhIjoidzdVQWlCN2R5enFQRk4wV2w1NWpEYlYvQ2FiRkt3aStXb0F3TEJwdkdleWhmMWx5YU9mSGN0V0NYQWtmZDJ4OW1yRHRvUFJzZ0FDYVlyVzB4ZS83azdrPSIsIml2UGFyYW1ldGVyU3BlYyI6IjVibUhPbk9xd1ZacjFITTciLCJtYXRlcmlhbFNldFNlcmlhbCI6MX0%3D&branch=master)

[![BoltOps Badge](https://img.boltops.com/boltops/badges/boltops-badge.png)](https://www.boltops.com)

This blueprint provisions an ElastiCache cluster.

* It can provision either a Memcached or Redis cluster.
* By default, it provisions a Memcached cluster.

Most resource properties are configurable with [Parameters](https://lono.cloud/docs/configs/params/). Additionally, all properties are generally configurable with [Variables](https://lono.cloud/docs/configs/params/). The blueprint is extremely flexible and customizable for your needs.  More info in the "Configure: More Details" section.

## Usage

1. Add blueprint to Gemfile
2. Configure: configs/elasticache values
3. Deploy blueprint

## Add

Add the blueprint to your lono project's `Gemfile`.

```ruby
gem "elasticache", git: "git@github.com:boltopspro/elasticache.git"
```

## Configure

Use the [lono seed](https://lono.cloud/reference/lono-seed/) command to generate a starter config params files.

    LONO_ENV=development lono seed elasticache
    LONO_ENV=production  lono seed elasticache

The files in `config/elasticache` folder will look something like this:

    configs/elasticache/
    ├── params
    │   ├── development.txt
    │   └── production.txt
    └── variables
        ├── development.rb
        └── production.rb

Configure the `configs/elasticache/params` and `configs/elasticache/variables` files.  Here's an example:

    Engine=memcached # or redis
    VpcId=vpc-111

## Deploy

Use the [lono cfn deploy](http://lono.cloud/reference/lono-cfn-deploy/) command to deploy.

    LONO_ENV=development lono cfn deploy elasticache --sure --no-wait
    LONO_ENV=production  lono cfn deploy elasticache --sure --no-wait

It takes about 5-10m to deploy the ElastiCache cluster. Times may vary.

If you are using One AWS Account, use these commands instead: [One Account](docs/one-account.md).

## Configure: More Details

### Provision Redis

To provision a Redis cluster instead of Memcached, configure the `Engine` parameter.  Example:

configs/elasticache/params/development.txt:

    Engine=redis # memcached or redis
    VpcId=vpc-111

### Security Groups

To assign existing security groups to the ElastiCache cluster, use the `VpcSecurityGroupIds` parameter.  Example:

    VpcSecurityGroupIds=sg-111,sg-222

Otherwise, the blueprint will create a managed security group for you.

### Auto-Managed Cache Subnet Group

You can assign an existing Cache Subnet Group with the `CacheSubnetGroupName` parameter.  Example:

    CacheSubnetGroupName=subnet-group-name

Or you can have the blueprint create a managed Cache Subnet Group by configuring the `Subnets` parameter. Example:

    Subnets=subnet-111,subnet-222

### Advanced Properties Customizations

Most of the [AWS::ElastiCache::CacheCluster properties](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-elasticache-cache-cluster.html) are configurable with [Parameters](https://lono.cloud/docs/configs/params/). Those are are not are configurable with [Variables](https://lono.cloud/docs/configs/variables/). Here's an example:

```ruby
@cache_cluster_properties = {
  SnapshotName: "my-snapshot",
}
```

You can view all configurable variables in [helpers/variables_helper.rb](app/helpers/variables_helper.rb).

The blueprint is written so that Variables take higher precedence than Parameters.
