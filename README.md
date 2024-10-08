# This is the GitHub repo to showcase my work on Aws
## chapter1: Aws cli/cloudformation

### creating Vpc Through CFT by Using paramaters

```
AWSTemplateFormatVersion: "2010-09-09"
Description: >
  This template creates VPC in ap-south-1 region
  Author: Murali
  Company: Havik

Parameters:
  VpcCidr:
    Type: String
    Default: 10.0.0.0/16
    Description: CIDR block for the VPC
  PublicSubnetCidr:
    Type: String
    Default: 10.0.1.0/24
    Description: CIDR block for the public subnet
  PrivateSubnetCidr:
    Type: String
    Default: 10.0.2.0/24
    Description: CIDR block for the private subnet
Resources:
  MyCfnVPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: !Ref VpcCidr
      Tags:
      - Key: Name
        Value: MyVPC
      - Key: Env 
        Value: Prod
      - Key: Department
        Value: DevOps        

  MyCfnInternetGateway:
    Type: 'AWS::EC2::InternetGateway'
    Properties:
      Tags:
        - Key: Name
          Value: MyIGW

  MyCfnAttachGateway:
    Type: 'AWS::EC2::VPCGatewayAttachment'
    Properties:
      VpcId: !Ref MyCfnVPC
      InternetGatewayId: !Ref MyCfnInternetGateway

  MyCfnPublicSubnet:
    Type: 'AWS::EC2::Subnet'
    Properties:
      VpcId: !Ref MyCfnVPC
      CidrBlock: !Ref PublicSubnetCidr
      AvailabilityZone: ap-south-1a
      Tags: 
        - Key: Name
          Value: PublicSubnet

  MyCfnPrivateSubnet:
    Type: 'AWS::EC2::Subnet'
    Properties:
      VpcId: !Ref MyCfnVPC
      CidrBlock: !Ref PrivateSubnetCidr
      AvailabilityZone: ap-south-1a
      Tags: 
        - Key: Name
          Value: PrivateSubnet

  PublicRouteTable:
    Type: 'AWS::EC2::RouteTable'
    Properties:
      VpcId: !Ref MyCfnVPC
      Tags: 
        - Key: Name
          Value: PublicRouteTable

  PrivateRouteTable:
    Type: 'AWS::EC2::RouteTable'
    Properties:
      VpcId: !Ref MyCfnVPC
      Tags: 
        - Key: Name
          Value: PrivateRouteTable

  PublicRoute:
    Type: 'AWS::EC2::Route'
    Properties:
      RouteTableId: !Ref PublicRouteTable
      DestinationCidrBlock: '0.0.0.0/0'
      GatewayId: !Ref MyCfnInternetGateway

  NatGatewayEIP:
    Type: 'AWS::EC2::EIP'
    Properties:
      Domain: vpc 

  NatGateway:
    Type: 'AWS::EC2::NatGateway'
    Properties:
      AllocationId: 
        Fn::GetAtt: 
          - NatGatewayEIP
          - AllocationId
      SubnetId: !Ref MyCfnPublicSubnet
      Tags: 
        - Key: Name
          Value: MyNATGateway 

  PrivateRoute:
    Type: 'AWS::EC2::Route'
    Properties:
      RouteTableId: !Ref PrivateRouteTable
      DestinationCidrBlock: '0.0.0.0/0'
      NatGatewayId: !Ref NatGateway 

  PublicSubnetRouteTableAssociation:
    Type: 'AWS::EC2::SubnetRouteTableAssociation'
    Properties:
      SubnetId: !Ref MyCfnPublicSubnet
      RouteTableId: !Ref PublicRouteTable  

  PrivateSubnetRouteTableAssociation:
    Type: 'AWS::EC2::SubnetRouteTableAssociation'
    Properties:
      SubnetId: !Ref MyCfnPrivateSubnet
      RouteTableId: !Ref PrivateRouteTable
```


In this chapter i will create the labs to show the AWS resourse creation through aws cli and cloud formation. 

1.The below is the command to Create VPC through AWS CLI:

Command:
''' 

            aws ec2 create-vpc \
                --cidr-block 10.0.0.0/16\
                --tag-specifications ResourceType-vpc, Tags- 
                [{Key-Name, Value-MyVpc}]


'''

Sample Output:

```


{
    "Vpc": {
        "CidrBlock": "10.0.0.0/16",
        "DhcpOptionsId": "dopt-5EXAMPLE",
        "State": "pending",
        "VpcId": "vpc-0a60eb65b4EXAMPLE",
        "OwnerId": "123456789012",
        "InstanceTenancy": "default",
        "Ipv6CidrBlockAssociationSet": [],
        "CidrBlockAssociationSet": [
            {
                "AssociationId": "vpc-cidr-assoc-07501b79ecEXAMPLE",
                "CidrBlock": "10.0.0.0/16",
                "CidrBlockState": {
                    "State": "associated"
                }
            }
        ],
        "IsDefault": false,
        "Tags": [
            {
                "Key": "Name",
                "Value": MyVpc"
            }
        ]
    }
}



```

2.The below is the command to delete vpc

```
aws ec2 delete-vpc --vpc-id <your-vpc-id>
```

3.The below command is used to create subnets to your vpc

```
aws ec2 create-subnet --vpc-id vpc-05a27c308645122dc --cidr-block 10.1.1.0/24 --availability-zone ap-south-1a
{
    "Subnet": {
        "AvailabilityZone": "ap-south-1a",
        "AvailabilityZoneId": "aps1-az1",
        "AvailableIpAddressCount": 251,
        "CidrBlock": "10.1.1.0/24",
        "DefaultForAz": false,
        "MapPublicIpOnLaunch": false,
        "State": "available",
        "SubnetId": "subnet-0ba98693e27d8849e",
        "VpcId": "vpc-12266637c",
        "OwnerId": "3814767763",
        "AssignIpv6AddressOnCreation": false,
        "Ipv6CidrBlockAssociationSet": [],
        "SubnetArn": "arn:aws:ec2:ap-south-1:381492281943:subnet/subnet-0ba98693e27d8849e",
        "EnableDns64": false,
        "Ipv6Native": false,
        "PrivateDnsNameOptionsOnLaunch": {
            "HostnameType": "ip-name",
            "EnableResourceNameDnsARecord": false,
            "EnableResourceNameDnsAAAARecord": false
        }
    }
}



```

4.The below commands are creating and attach internet gateway to your vpc

```
aws ec2 create-internet-gateway

aws ec2 create-internet-gateway
{
    "InternetGateway": {
        "Attachments": [],
        "InternetGatewayId": "igw-0cb178f7d6d6e13ce",
        "OwnerId": "38145757943",
        "Tags": []
    }
}


aws ec2 attach-internet-gateway --vpc-id vpc-xxxxxxxx --internet-gateway-id igw-xxxxxxxx

aws ec2 attach-internet-gateway --vpc-id vpc-05a27c308hjj78dc --internet-gateway-id igw-0cb178f7d6d6e13ce

```

5.The below command is used to create route table

```
aws ec2 create-route-table --vpc-id vpc-xxxxxxxx

aws ec2 create-route-table --vpc-id vpc-05a27c308645122dc
{
    "RouteTable": {
        "Associations": [],
        "PropagatingVgws": [],
        "RouteTableId": "rtb-0750f2d6ef8e9f287",
        "Routes": [
            {
                "DestinationCidrBlock": "10.1.0.0/16",
                "GatewayId": "local",
                "Origin": "CreateRouteTable",
                "State": "active"
            }
        ],
        "Tags": [],
        "VpcId": "vpc-05a27chds5122dc",
        "OwnerId": "38667681943"
    },
    "ClientToken": "ee49669d-b5a5-4c6f-979b-2cdd38539163"
}


```

6.The below is the command to create the route to the internet-gateway

```
aws ec2 create-route --route-table-id rtb-xxxxxxxx --destination-cidr-block 0.0.0.0/0 --gateway-id igw-xxxxxxxx
```

7.The below command is used to Associate the Public Subnet with the Route Table

```
aws ec2 associate-route-table --subnet-id subnet-xxxxxxxx --route-table-id rtb-xxxxxxxx
```





# Markdown Cheatsheet

This is intended as a quick reference and showcase. For more complete info, see John Gruber's original spec and the Github-flavored Markdown info page.

Note that there is also a Cheatsheet specific to Markdown Here if that's what you're looking for. You can also check out more Markdown tools.

## Table of Contents
Headers
Emphasis
Lists
Links
Images
Code and Syntax Highlighting
Footnotes
Tables
Blockquotes
Inline HTML
Horizontal Rule
Line Breaks
YouTube Videos

## Headers
```
# H1
## H2
### H3
#### H4
##### H5
###### H6

Alternatively, for H1 and H2, an underline-ish style:

Alt-H1
======

Alt-H2
------
```

# H1
## H2
### H3
#### H4
##### H5
###### H6

Alternatively, for H1 and H2, an underline-ish style:

Alt-H1
======

Alt-H2
------

## Emphasis

```
Emphasis, aka italics, with *asterisks* or _underscores_.

Strong emphasis, aka bold, with **asterisks** or __underscores__.

Combined emphasis with **asterisks and _underscores_**.

Strikethrough uses two tildes. ~~Scratch this.~~
```
Emphasis, aka italics, with asterisks or underscores.

Strong emphasis, aka bold, with asterisks or underscores.

Combined emphasis with asterisks and underscores.

Strikethrough uses two tildes. Scratch this.


## Lists
(In this example, leading and trailing spaces are shown with with dots: ⋅)
```
1. First ordered list item
2. Another item
⋅⋅* Unordered sub-list. 
1. Actual numbers don't matter, just that it's a number
⋅⋅1. Ordered sub-list
4. And another item.

⋅⋅⋅You can have properly indented paragraphs within list items. Notice the blank line above, and the leading spaces (at least one, but we'll use three here to also align the raw Markdown).

⋅⋅⋅To have a line break without a paragraph, you will need to use two trailing spaces.⋅⋅
⋅⋅⋅Note that this line is separate, but within the same paragraph.⋅⋅
⋅⋅⋅(This is contrary to the typical GFM line break behaviour, where trailing spaces are not required.)

* Unordered list can use asterisks
- Or minuses
+ Or pluses
```
First ordered list item
Another item
Unordered sub-list.
Actual numbers don't matter, just that it's a number

Ordered sub-list

And another item.

You can have properly indented paragraphs within list items. Notice the blank line above, and the leading spaces (at least one, but we'll use three here to also align the raw Markdown).

To have a line break without a paragraph, you will need to use two trailing spaces.
Note that this line is separate, but within the same paragraph.
(This is contrary to the typical GFM line break behaviour, where trailing spaces are not required.)

Unordered list can use asterisks
Or minuses
Or pluses


## Links
```
[I'm an inline-style link](https://www.google.com)

[I'm an inline-style link with title](https://www.google.com "Google's Homepage")

[I'm a reference-style link][Arbitrary case-insensitive reference text]

[I'm a relative reference to a repository file](https://github.com/Murali114/DevOPsm?tab=readme-ov-file)

[You can use numbers for reference-style link definitions][1]

Or leave it empty and use the [link text itself].

URLs and URLs in angle brackets will automatically get turned into links. 
http://www.example.com or <http://www.example.com> and sometimes 
example.com (but not on Github, for example).

Some text to show that the reference links can follow later.

[arbitrary case-insensitive reference text]: https://www.mozilla.org
[1]: http://slashdot.org
[link text itself]: http://www.reddit.com
```
[I'm an inline-style link](https://www.youtube.com/)

[I'm an inline-style link with title](https://www.google.com "Google's Homepage")

[I'm a reference-style link][Arbitrary case-insensitive reference text]

[I'm a relative reference to a repository file](https://github.com/Murali114/DevOPsm?tab=readme-ov-file)

[You can use numbers for reference-style link definitions][1]

Or leave it empty and use the [link text itself].

URLs and URLs in angle brackets will automatically get turned into links. 
http://www.example.com or <http://www.example.com> and sometimes 
example.com (but not on Github, for example).

Some text to show that the reference links can follow later.

[arbitrary case-insensitive reference text]: https://www.mozilla.org
[1]: http://slashdot.org
[link text itself]: http://www.reddit.com

You can use numbers for reference-style link definitions

Or leave it empty and use the link text itself.

URLs and URLs in angle brackets will automatically get turned into links. http://www.example.com or http://www.example.com and sometimes example.com (but not on Github, for example).

Some text to show that the reference links can follow later.

## Images 

```
Here's our logo (hover to see the title text):
Inline-style: 
![alt text](https://www.svgrepo.com/show/google-g-2015-logo.svg "Logo Title Text 1")
```

Inline-style: 
![alt text](https://www.svgrepo.com/show/303552/google-g-2015-logo.svg "Logo Title Text 1")

## Code and Syntax Highlighting
Code blocks are part of the Markdown spec, but syntax highlighting isn't. However, many renderers -- like Github's and Markdown Here -- support syntax highlighting. Which languages are supported and how those language names should be written will vary from renderer to renderer. Markdown Here supports highlighting for dozens of languages (and not-really-languages, like diffs and HTTP headers); to see the complete list, and how to write the language names, see the highlight.js demo page.
```
Inline `code` has `back-ticks around` it.
```
Blocks of code are either fenced by lines with three back-ticks ```, or are indented with four spaces. I recommend only using the fenced code blocks -- they're easier and only they support syntax highlighting.
```
```javascript
var s = "JavaScript syntax highlighting";
alert(s);
```
 
```python
s = "Python syntax highlighting"
print s
```
 
```
No language indicated, so no syntax highlighting. 
But let's throw in a <b>tag</b>.
```
```
var s = "JavaScript syntax highlighting";
alert(s);
```
```
s = "Python syntax highlighting"
print s
```
```
No language indicated, so no syntax highlighting in Markdown Here (varies on Github). 
But let's throw in a <b>tag</b>.
```
## Footnotes
Footnotes aren't part of the core Markdown spec, but they supported by GFM.

```
Here is a simple footnote[^1].

A footnote can also have multiple lines[^2].  

You can also use words, to fit your writing style more closely[^note].

[^1]: My reference.
[^2]: Every new line should be prefixed with 2 spaces.  
  This allows you to have a footnote with multiple lines.
[^note]:
    Named footnotes will still render with numbers instead of the text but allow easier identification and linking.  
    This footnote also has been made with a different syntax using 4 spaces for new lines.
```
### Tables
Tables aren't part of the core Markdown spec, but they are part of GFM and Markdown Here supports them. They are an easy way of adding tables to your email -- a task that would otherwise require copy-pasting from another application.
```
Colons can be used to align columns.

| Tables        | Are           | Cool  |
| ------------- |:-------------:| -----:|
| col 3 is      | right-aligned | $1600 |
| col 2 is      | centered      |   $12 |
| zebra stripes | are neat      |    $1 |

There must be at least 3 dashes separating each header cell.
The outer pipes (|) are optional, and you don't need to make the 
raw Markdown line up prettily. You can also use inline Markdown.

Markdown | Less | Pretty
--- | --- | ---
*Still* | `renders` | **nicely**
1 | 2 | 3
```
Colons can be used to align columns.
Colons can be used to align columns.

| Tables        | Are           | Cool  |
| ------------- |:-------------:| -----:|
| col 3 is      | right-aligned | $1600 |
| col 2 is      | centered      |   $12 |
| zebra stripes | are neat      |    $1 |

There must be at least 3 dashes separating each header cell.
The outer pipes (|) are optional, and you don't need to make the 
raw Markdown line up prettily. You can also use inline Markdown.

Markdown | Less | Pretty
--- | --- | ---
*Still* | `renders` | **nicely**
1 | 2 | 3

## Blockquotes
```
> Blockquotes are very handy in email to emulate reply text.
> This line is part of the same quote.

Quote break.

> This is a very long line that will still be quoted properly when it wraps. Oh boy let's keep writing to make sure this is long enough to actually wrap for everyone. Oh, you can *put* **Markdown** into a blockquote. 
```
Blockquotes are very handy in email to emulate reply text. This line is part of the same quote.

##### Quote break.
This is a very long line that will still be quoted properly when it wraps. Oh boy let's keep writing to make sure this is long enough to actually wrap for everyone. Oh, you can put Markdown into a blockquote.

## Inline HTML
You can also use raw HTML in your Markdown, and it'll mostly work pretty well.
```
<dl>
  <dt>Definition list</dt>
  <dd>Is something people use sometimes.</dd>

  <dt>Markdown in HTML</dt>
  <dd>Does *not* work **very** well. Use HTML <em>tags</em>.</dd>
</dl>
```
#### Definition list
Is something people use sometimes.

#### Markdown in HTML
Does *not* work **very** well. Use HTML tags.