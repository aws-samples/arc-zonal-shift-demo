## Application Recovery Controller Zonal Shift Demo

This repo provides a demonstration template which you can use to explore ARC's zonal shift.

It creates: 

* a 3-AZ Network Load Balancer, with cross-zone load balancing disabled, in a 3-AZ VPC
* an autoscaling group, which creates 2x hosts per AZ, each of which runs the Apache web server, serving a 1MB file
* 1x CloudWatch Synthetics canaries, polling the file via the regional ELB, recording latency
* 3x CloudWatch Synthetics canaries, polling the file via the ELB's zonal endpoints and recording latency
* A CloudWatch dashboard which displays the performance as seen by customers, and via each AZ.
* 2x Fault Injection Simulator experiments, one of which breaks one AZ completely by disabling Apaache and another which temporarily injects packet loss in one AZ.

These can be used as examples to create both hard and grey failures and use
ELB health checks or Zonal Shift to recover from them.

## How to deploy the template

Use the command below, but swap in a valid email address

% aws cloudformation create-stack \
        --region $REGION \
        --stack-name arc-zonal-shift-demo \
        --template-body file://ARC-Zonal-Shift-Demo.json  \
        --capabilities CAPABILITY_NAMED_IAM \
        --parameters 'ParameterKey=OperatorEMail,ParameterValue=$EMAIL'

## How to start an experiment

First list the experiment templates available...

% aws fis list-experiment-templates 

Identify the experiment you wish to start (e.g. PacketLossOnInstancesIn-AZ-B), then use the experiment ID:

% aws fis start-experiment --experiment-template-id EXT4FjG6mLPaErxG


## How to perform a zonal shift

Fill in the ARN and AZ you want to shift away from below.

% aws arc-zonal-shift start-zonal-shift \
		--away-from apse2-az1 \
		--expires-in 30m \
		--resource-identifier "arn:aws:elasticloadbalancing:ap-southeast-2:123456789012:loadbalancer/net/zonal-shift-demo/xxxxxxxxxxxxxxxx" \
		--comment "shift away from AZ B"




## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the MIT-0 License. See the LICENSE file.

