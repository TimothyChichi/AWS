# AWS Command Line Interface #
`$ aws configure` <br>
設定 AWS 的 Access Key ID <br>
設定 AWS 的 Secret Access Key <br>
設定 Default Region name <br>
設定輸出格式 (JSON, text, table)

取得 Access Key ID & Secret Access Key 的方式，要點開帳號旁的下拉選單，選擇 Security Credentials，進入後其中有一項 Access Keys，點開可管理 Access Keys，如果還沒有 Access Key ，可以 create 一組，由於只能下載一次，下載後務必妥善保存，下載回來的 CSV 檔案中即會得到這兩個資訊。

> Access key 只能下載一次，下載後務必妥善保存。
>在 AWS 中，每個 region 都是彼此獨立的，所以 region 設定務必要確定。

`$ aws ec2 describe-instances`
這個指令可以察看該 region 中所有的 instances，例如：

```
{
    "Reservations": [
        {
            "Instances": [
                {
                    "Monitoring": {
                        "State": "disabled"
                    },
                    "PublicDnsName": "ec2-18-216-173-162.us-east-2.compute.amazonaws.com",
                    "Platform": "windows",
                    "State": {
                        "Code": 16,
                        "Name": "running"
                    },
                    "EbsOptimized": false,
                    "LaunchTime": "2017-11-21T09:09:26.000Z",
                    "PublicIpAddress": "18.216.173.162",
                    "PrivateIpAddress": "172.31.36.253",
                    "ProductCodes": [],
                    "VpcId": "vpc-5b1afb33",
                    "StateTransitionReason": "",
                    "InstanceId": "i-0d85c3ea4a978df26",
                    "EnaSupport": true,
                    "ImageId": "ami-80765ae5",
                    "PrivateDnsName": "ip-172-31-36-253.us-east-2.compute.internal",
                    "KeyName": "InterpreterTest",
                    "SecurityGroups": [
                        {
                            "GroupName": "launch-wizard-2",
                            "GroupId": "sg-73cc991b"
                        }
                    ],
                    "ClientToken": "",
                    "SubnetId": "subnet-8a3258c7",
                    "InstanceType": "t2.micro",
                    "NetworkInterfaces": [
                        {
                            "Status": "in-use",
                            "MacAddress": "0a:b1:a6:13:c2:ee",
                            "SourceDestCheck": true,
                            "VpcId": "vpc-5b1afb33",
                            "Description": "",
                            "NetworkInterfaceId": "eni-36d20e1d",
                            "PrivateIpAddresses": [
                                {
                                    "PrivateDnsName": "ip-172-31-36-253.us-east-2.compute.internal",
                                    "PrivateIpAddress": "172.31.36.253",
                                    "Primary": true,
                                    "Association": {
                                        "PublicIp": "18.216.173.162",
                                        "PublicDnsName": "ec2-18-216-173-162.us-east-2.compute.amazonaws.com",
                                        "IpOwnerId": "amazon"
                                    }
                                }
                            ],
                            "PrivateDnsName": "ip-172-31-36-253.us-east-2.compute.internal",
                            "Attachment": {
                                "Status": "attached",
                                "DeviceIndex": 0,
                                "DeleteOnTermination": true,
                                "AttachmentId": "eni-attach-afd58944",
                                "AttachTime": "2017-11-21T06:54:42.000Z"
                            },
                            "Groups": [
                                {
                                    "GroupName": "launch-wizard-2",
                                    "GroupId": "sg-73cc991b"
                                }
                            ],
                            "Ipv6Addresses": [],
                            "OwnerId": "649047321819",
                            "PrivateIpAddress": "172.31.36.253",
                            "SubnetId": "subnet-8a3258c7",
                            "Association": {
                                "PublicIp": "18.216.173.162",
                                "PublicDnsName": "ec2-18-216-173-162.us-east-2.compute.amazonaws.com",
                                "IpOwnerId": "amazon"
                            }
                        }
                    ],
                    "SourceDestCheck": true,
                    "Placement": {
                        "Tenancy": "default",
                        "GroupName": "",
                        "AvailabilityZone": "us-east-2c"
                    },
                    "Hypervisor": "xen",
                    "BlockDeviceMappings": [
                        {
                            "DeviceName": "/dev/sda1",
                            "Ebs": {
                                "Status": "attached",
                                "DeleteOnTermination": true,
                                "VolumeId": "vol-00d103342af27ed11",
                                "AttachTime": "2017-11-21T06:54:42.000Z"
                            }
                        }
                    ],
                    "Architecture": "x86_64",
                    "RootDeviceType": "ebs",
                    "RootDeviceName": "/dev/sda1",
                    "VirtualizationType": "hvm",
                    "AmiLaunchIndex": 0
                }
            ],
            "ReservationId": "r-0cda1987f1cfb8014",
            "Groups": [],
            "OwnerId": "649047321819"
        }
    ]
}
```
以上是目前只產生一個 instance ，並且這個 instance 是跑 Windows server 2012 R2 ，的結果，需要注意的是這裡有幾個 instances 就會跑出幾倍的數量，instances 數量多的時候最好要清楚 instance ID 。以這個例子而言，可以看見 instance-ID 為 `"InstanceId": "i-0d85c3ea4a978df26"` ，可以透過以下指令來針對特定 instance 作描述：
`$ aws ec2 describe-instances --instance-ids "INSTANCE_ID"`
>Instance ID 可以數個 ID，使用 " " 隔開。

從以上例子我們看到 instance 會產生幾個關鍵的資訊，例如： OS type, Public domain name, Public/Private IP, MacAddressm, Hypervisor, Architecture, 以及 Instance type ， instance type 這個資訊可以透過查表得知使用的 vCPU, memory 等資訊。

我們可以針對 instance 的 image 做更細部的呼叫，從上可以查到 image ID `"ImageId": "ami-80765ae5"` ，可以透過以下指令呼叫：

`$ aws ec2 describe-images --image-ids "IMAGE_ID"`

不建議直接呼叫 `aws ec2 describe-images` ，這個指令不指定 image 會產生太多其他的 image 資料。以這個 case 我可以呼叫 `$ aws ec2 describe-images --image-ids "ami-80765ae5"` 便得到

```
{
    "Images": [
        {
            "VirtualizationType": "hvm",
            "Description": "Microsoft Windows Server 2012 R2 RTM 64-bit Locale English AMI provided by Amazon",
            "Hypervisor": "xen",
            "ImageOwnerAlias": "amazon",
            "EnaSupport": true,
            "SriovNetSupport": "simple",
            "ImageId": "ami-80765ae5",
            "Platform": "windows",
            "State": "available",
            "BlockDeviceMappings": [
                {
                    "DeviceName": "/dev/sda1",
                    "Ebs": {
                        "Encrypted": false,
                        "DeleteOnTermination": true,
                        "VolumeType": "gp2",
                        "VolumeSize": 30,
                        "SnapshotId": "snap-0883ded5b0692277d"
                    }
                },
                {
                    "DeviceName": "xvdca",
                    "VirtualName": "ephemeral0"
                },
                {
                    "DeviceName": "xvdcb",
                    "VirtualName": "ephemeral1"
                },
                {
                    "DeviceName": "xvdcc",
                    "VirtualName": "ephemeral2"
                },
                {
                    "DeviceName": "xvdcd",
                    "VirtualName": "ephemeral3"
                },
                {
                    "DeviceName": "xvdce",
                    "VirtualName": "ephemeral4"
                },
                {
                    "DeviceName": "xvdcf",
                    "VirtualName": "ephemeral5"
                },
                {
                    "DeviceName": "xvdcg",
                    "VirtualName": "ephemeral6"
                },
                {
                    "DeviceName": "xvdch",
                    "VirtualName": "ephemeral7"
                },
                {
                    "DeviceName": "xvdci",
                    "VirtualName": "ephemeral8"
                },
                {
                    "DeviceName": "xvdcj",
                    "VirtualName": "ephemeral9"
                },
                {
                    "DeviceName": "xvdck",
                    "VirtualName": "ephemeral10"
                },
                {
                    "DeviceName": "xvdcl",
                    "VirtualName": "ephemeral11"
                },
                {
                    "DeviceName": "xvdcm",
                    "VirtualName": "ephemeral12"
                },
                {
                    "DeviceName": "xvdcn",
                    "VirtualName": "ephemeral13"
                },
                {
                    "DeviceName": "xvdco",
                    "VirtualName": "ephemeral14"
                },
                {
                    "DeviceName": "xvdcp",
                    "VirtualName": "ephemeral15"
                },
                {
                    "DeviceName": "xvdcq",
                    "VirtualName": "ephemeral16"
                },
                {
                    "DeviceName": "xvdcr",
                    "VirtualName": "ephemeral17"
                },
                {
                    "DeviceName": "xvdcs",
                    "VirtualName": "ephemeral18"
                },
                {
                    "DeviceName": "xvdct",
                    "VirtualName": "ephemeral19"
                },
                {
                    "DeviceName": "xvdcu",
                    "VirtualName": "ephemeral20"
                },
                {
                    "DeviceName": "xvdcv",
                    "VirtualName": "ephemeral21"
                },
                {
                    "DeviceName": "xvdcw",
                    "VirtualName": "ephemeral22"
                },
                {
                    "DeviceName": "xvdcx",
                    "VirtualName": "ephemeral23"
                },
                {
                    "DeviceName": "xvdcy",
                    "VirtualName": "ephemeral24"
                },
                {
                    "DeviceName": "xvdcz",
                    "VirtualName": "ephemeral25"
                }
            ],
            "Architecture": "x86_64",
            "ImageLocation": "amazon/Windows_Server-2012-R2_RTM-English-64Bit-Base-2017.10.13",
            "RootDeviceType": "ebs",
            "OwnerId": "801119661308",
            "RootDeviceName": "/dev/sda1",
            "CreationDate": "2017-10-12T08:10:36.000Z",
            "Public": true,
            "ImageType": "machine",
            "Name": "Windows_Server-2012-R2_RTM-English-64Bit-Base-2017.10.13"
        }
    ]
}
```

可以看到 image 的一些細部資訊，例如 Vrtualization type, OS version, image type 等。


`$ aws ec2 describe-network-interfaces --network-interface-ids "NETWORK_INTERFACE_ID"`

此指令可查詢 network interface 細部資訊如：
```
{
    "NetworkInterfaces": [
        {
            "Status": "in-use",
            "MacAddress": "0a:b1:a6:13:c2:ee",
            "SourceDestCheck": true,
            "AvailabilityZone": "us-east-2c",
            "Description": "",
            "NetworkInterfaceId": "eni-36d20e1d",
            "PrivateIpAddresses": [
                {
                    "PrivateDnsName": "ip-172-31-36-253.us-east-2.compute.internal",
                    "PrivateIpAddress": "172.31.36.253",
                    "Primary": true,
                    "Association": {
                        "PublicIp": "18.216.173.162",
                        "PublicDnsName": "ec2-18-216-173-162.us-east-2.compute.amazonaws.com",
                        "IpOwnerId": "amazon"
                    }
                }
            ],
            "RequesterManaged": false,
            "PrivateDnsName": "ip-172-31-36-253.us-east-2.compute.internal",
            "VpcId": "vpc-5b1afb33",
            "InterfaceType": "interface",
            "Attachment": {
                "Status": "attached",
                "DeviceIndex": 0,
                "AttachTime": "2017-11-21T06:54:42.000Z",
                "InstanceId": "i-0d85c3ea4a978df26",
                "DeleteOnTermination": true,
                "AttachmentId": "eni-attach-afd58944",
                "InstanceOwnerId": "649047321819"
            },
            "Groups": [
                {
                    "GroupName": "launch-wizard-2",
                    "GroupId": "sg-73cc991b"
                }
            ],
            "Ipv6Addresses": [],
            "OwnerId": "649047321819",
            "PrivateIpAddress": "172.31.36.253",
            "SubnetId": "subnet-8a3258c7",
            "TagSet": [],
            "Association": {
                "PublicIp": "18.216.173.162",
                "PublicDnsName": "ec2-18-216-173-162.us-east-2.compute.amazonaws.com",
                "IpOwnerId": "amazon"
            }
        }
    ]
}
```

另外一個與網路相關的描述指令為

`$ aws ec2 describe-vpcs --vpc-ids "VPC_ID"`

輸出結果大致如下：
```
{
    "Vpcs": [
        {
            "VpcId": "vpc-5b1afb33",
            "InstanceTenancy": "default",
            "CidrBlockAssociationSet": [
                {
                    "AssociationId": "vpc-cidr-assoc-1549457c",
                    "CidrBlock": "172.31.0.0/16",
                    "CidrBlockState": {
                        "State": "associated"
                    }
                }
            ],
            "State": "available",
            "DhcpOptionsId": "dopt-1e19ee76",
            "CidrBlock": "172.31.0.0/16",
            "IsDefault": true
        }
    ]
}

```

這些結果許多都有重覆，所以一般而言，我建議使用 `describe-instances` 列印出 instance 資訊，配上 `describe-images` 來找出 image 詳細資料，應該就足夠了。

---
另外，AWS 提供了 Cloud Watch ，透過 cloud watch 可以監控過去某段特定時間的使用量，透過用戶的使用量可以給予 migration 時的建議，官方文件中範例指令大概長成這樣

`aws cloudwatch get-metric-statistics --metric-name Buffers --namespace MyNameSpace --dimensions Name=InstanceID,Value=i-abcdef Name=InstanceType,Value=m1.small --start-time 2016-10-15T04:00:00Z --end-time 2016-10-19T07:00:00Z --statistics Average --period 60`

輸出結果如下：
```
{
    "Datapoints": [
        {
            "Timestamp": "2014-04-09T11:18:00Z",
            "Maximum": 44.79,
            "Unit": "Percent"
        },
        {
            "Timestamp": "2014-04-09T20:18:00Z",
            "Maximum": 47.92,
            "Unit": "Percent"
        },
        {
            "Timestamp": "2014-04-09T19:18:00Z",
            "Maximum": 50.85,
            "Unit": "Percent"
        },
        {
            "Timestamp": "2014-04-09T09:18:00Z",
            "Maximum": 47.92,
            "Unit": "Percent"
        },
        {
            "Timestamp": "2014-04-09T03:18:00Z",
            "Maximum": 76.84,
            "Unit": "Percent"
        },
        {
            "Timestamp": "2014-04-09T21:18:00Z",
            "Maximum": 48.96,
            "Unit": "Percent"
        },
        {
            "Timestamp": "2014-04-09T14:18:00Z",
            "Maximum": 47.92,
            "Unit": "Percent"
        },
        {
            "Timestamp": "2014-04-09T08:18:00Z",
            "Maximum": 47.92,
            "Unit": "Percent"
        },
        {
            "Timestamp": "2014-04-09T16:18:00Z",
            "Maximum": 45.55,
            "Unit": "Percent"
        },
        {
            "Timestamp": "2014-04-09T06:18:00Z",
            "Maximum": 47.92,
            "Unit": "Percent"
        },
        {
            "Timestamp": "2014-04-09T13:18:00Z",
            "Maximum": 45.08,
            "Unit": "Percent"
        },
        {
            "Timestamp": "2014-04-09T05:18:00Z",
            "Maximum": 47.92,
            "Unit": "Percent"
        },
        {
            "Timestamp": "2014-04-09T18:18:00Z",
            "Maximum": 46.88,
            "Unit": "Percent"
        },
        {
            "Timestamp": "2014-04-09T17:18:00Z",
            "Maximum": 52.08,
            "Unit": "Percent"
        },
        {
            "Timestamp": "2014-04-09T07:18:00Z",
            "Maximum": 47.92,
            "Unit": "Percent"
        },
        {
            "Timestamp": "2014-04-09T02:18:00Z",
            "Maximum": 51.23,
            "Unit": "Percent"
        },
        {
            "Timestamp": "2014-04-09T12:18:00Z",
            "Maximum": 47.67,
            "Unit": "Percent"
        },
        {
            "Timestamp": "2014-04-08T23:18:00Z",
            "Maximum": 46.88,
            "Unit": "Percent"
        },
        {
            "Timestamp": "2014-04-09T10:18:00Z",
            "Maximum": 51.91,
            "Unit": "Percent"
        },
        {
            "Timestamp": "2014-04-09T04:18:00Z",
            "Maximum": 47.13,
            "Unit": "Percent"
        },
        {
            "Timestamp": "2014-04-09T15:18:00Z",
            "Maximum": 48.96,
            "Unit": "Percent"
        },
        {
            "Timestamp": "2014-04-09T00:18:00Z",
            "Maximum": 48.16,
            "Unit": "Percent"
        },
        {
            "Timestamp": "2014-04-09T01:18:00Z",
            "Maximum": 49.18,
            "Unit": "Percent"
        }
    ],
    "Label": "CPUUtilization"
}
```
而官方的文件中已經描述了可以提供監控的 metrics ，如 CPU 用量， Disk 用量， Network in/out 流量等等。

---
# Reference #

[1] [AWS CLI Document](http://docs.aws.amazon.com/cli/latest/reference/)

[2] [Instance Type](https://aws.amazon.com/tw/ec2/pricing/on-demand/)

[3] [Amazon CloudWatch](http://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/ec2-metricscollected.html)
