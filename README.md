# NextJS to AWS with OpenNext & SST

## NextJS

https://nextjs.org/docs

## OpenNext

https://open-next.js.org/

"OpenNext takes the Next.js build output and converts it into a package that can be deployed to any functions as a service platform. As of now only AWS Lambda is supported."

## SST

https://docs.sst.dev/what-is-sst
https://docs.sst.dev/start/nextjs

"SST is a framework that makes it easy to build modern full-stack applications on AWS."

https://sst.dev/chapters/who-is-this-guide-for.html

"This guide is meant for full-stack developers or developers that would like to build full stack serverless applications."

## Other Ways

"Deploys a NextJs static site with server-side rendering and API support. Uses AWS lambda and CloudFront."
https://github.com/jetbridge/cdk-nextjs

"This module deploys a next.js website using Open Next to AWS utilising lambda, S3 and CloudFront."
https://github.com/RJPearson94/terraform-aws-open-next
https://github.com/RJPearson94/terraform-aws-open-next-examples/

### Frontend

Start by defining, in code, the frontend you are using. SST supports the following.
Next.js

```ts
new NextjsSite(stack, "site", {
  customDomain: "my-next-app.com",
});
```

Behind the scenes, `NextjsSite` will create the infrastructure to host your serverless Next.js app on AWS. You can also configure it with your custom domain.

---

## Steps

Create NextJS App

`npx create-next-app@latest`

Initialize SST

`npx create-sst@latest`

Install Dependencies

`npm install`

Disable the [Anonymous Telemetry](https://docs.sst.dev/anonymous-telemetry)

`npx sst telemetry disable`

Run in Development

`npm run dev`

#### First Run:

```bash
$ npm run dev

> next-sst@0.1.0 dev
> sst bind next dev

Please enter a name you’d like to use for your personal stage. Or hit enter to use Baz:

✔ Deploying bootstrap stack, this only needs to happen once
Warning: The site has not been deployed. Some resources might not be available.

   ▲ Next.js 14.0.4
   - Local:        http://localhost:3000

 ✓ Ready in 4.9s
 ○ Compiling / ...
 ✓ Compiled / in 4.6s (522 modules)
 ✓ Compiled in 261ms (257 modules)

```

#### Subsequent Run:

```bash
$ npm run dev

> next-sst@0.1.0 dev
> sst bind next dev

Warning: The site has not been deployed. Some resources might not be available.

   ▲ Next.js 14.0.4
   - Local:        http://localhost:3000

 ✓ Ready in 3.1s
```

---

### Add File Upload to the NextJS App

Add an S3 bucket to your `sst.config.ts`

Import the Bucket Construct

`import { Bucket } from "sst/constructs"`

Initialise a new Bucket

`const bucket = new Bucket(stack, "public");`

Bind the Bucket to the NextJS App

`const site = new NextjsSite(stack, "site", { bind: [bucket] });`

```ts
// sst.config.ts
import { SSTConfig } from "sst";
import { NextjsSite, Bucket } from "sst/constructs";

export default {
  config(_input) {
    return {
      name: "next-sst",
      region: "eu-west-2",
    };
  },
  stacks(app) {
    app.stack(function Site({ stack }) {
      const bucket = new Bucket(stack, "public");
      const site = new NextjsSite(stack, "site", { bind: [bucket] });

      stack.addOutputs({
        SiteUrl: site.url,
      });
    });
  },
} satisfies SSTConfig;
```

---

### Deploy to AWS

`npx sst deploy --stage prod`

```bash
$ npx sst deploy --stage prod
✔ Deploying bootstrap stack, this only needs to happen once
SST v2.38.4

➜  App:     next-sst
   Stage:   prod
   Region:  ~~~~~~~~
   Account: ~~~~~~~~

Next.js v14.0.4
OpenNext v2.3.1

┌─────────────────────────────────┐
│ OpenNext — Building Next.js app │
└─────────────────────────────────┘


> next-sst@0.1.0 build
> next build

   ▲ Next.js 14.0.4

 ✓ Creating an optimized production build
 ✓ Compiled successfully
 ✓ Linting and checking validity of types
 ✓ Collecting page data
 ✓ Generating static pages (5/5)
 ✓ Collecting build traces
 ✓ Finalizing page optimization

Route (app)                              Size     First Load JS
┌ ○ /                                    5.13 kB          87 kB
└ ○ /_not-found                          875 B          82.7 kB
+ First Load JS shared by all            81.8 kB
  ├ chunks/938-5e061ba0d46125b1.js       26.7 kB
  ├ chunks/fd9d1056-735d320b4b8745cb.js  53.3 kB
  ├ chunks/main-app-5da9ff8bbac14e5e.js  220 B
  └ chunks/webpack-5887780b433c6ac0.js   1.64 kB


○  (Static)  prerendered as static content


┌──────────────────────────────┐
│ OpenNext — Generating bundle │
└──────────────────────────────┘

Bundling static assets...
Bundling cache assets...
Bundling server function...

┌──────────────────────────────┐
│ OpenNext — Generating bundle │
└──────────────────────────────┘

Bundling static assets...
Bundling cache assets...
Bundling server function...
Bundling revalidation function...
Bundling image optimization function...
Bundling warmer function...
✔  Building...

|  Site PUBLISH_ASSETS_COMPLETE
|  Site site/Distribution/Origin3/S3Origin AWS::CloudFront::CloudFrontOriginAccessIdentity CREATE_COMPLETE
|  Site site/RevalidationQueue AWS::SQS::Queue CREATE_COMPLETE
|  Site site/CloudFrontFunction AWS::CloudFront::Function CREATE_COMPLETE
|  Site site/ServerCache AWS::CloudFront::CachePolicy CREATE_COMPLETE
|  Site site/ImageFunction/ServiceRole AWS::IAM::Role CREATE_COMPLETE
|  Site LogRetentionaae0aa3c5b4d4f87b02d85b201efdd8a/ServiceRole AWS::IAM::Role CREATE_COMPLETE
|  Site CustomResourceHandler/ServiceRole AWS::IAM::Role CREATE_COMPLETE
|  Site Custom::S3AutoDeleteObjectsCustomResourceProvider AWS::IAM::Role CREATE_COMPLETE
|  Site site/RevalidationFunction/ServiceRole AWS::IAM::Role CREATE_COMPLETE
|  Site site/RevalidationTable AWS::DynamoDB::GlobalTable CREATE_COMPLETE
|  Site site/S3Bucket AWS::S3::Bucket CREATE_COMPLETE
|  Site public/Bucket AWS::S3::Bucket CREATE_COMPLETE
|  Site CustomResourceHandler AWS::Lambda::Function CREATE_COMPLETE
|  Site public/Parameter_bucketName AWS::SSM::Parameter CREATE_COMPLETE
|  Site public/Bucket/Policy AWS::S3::BucketPolicy CREATE_COMPLETE
|  Site site/S3Bucket/Policy AWS::S3::BucketPolicy CREATE_COMPLETE
|  Site LogRetentionaae0aa3c5b4d4f87b02d85b201efdd8a/ServiceRole/DefaultPolicy AWS::IAM::Policy CREATE_COMPLETE
|  Site site/ServerFunction/AssetReplacerPolicy AWS::IAM::Policy CREATE_COMPLETE
|  Site site/RevalidationFunction/ServiceRole/DefaultPolicy AWS::IAM::Policy CREATE_COMPLETE
|  Site Custom::S3AutoDeleteObjectsCustomResourceProvider AWS::Lambda::Function CREATE_COMPLETE
|  Site site/S3Bucket/AutoDeleteObjectsCustomResource Custom::S3AutoDeleteObjects CREATE_COMPLETE
|  Site site/ServerFunction/AssetReplacer Custom::AssetReplacer CREATE_COMPLETE
|  Site LogRetentionaae0aa3c5b4d4f87b02d85b201efdd8a AWS::Lambda::Function CREATE_COMPLETE
|  Site site/RevalidationFunction AWS::Lambda::Function CREATE_COMPLETE
|  Site site/S3AssetUploaderPolicy AWS::IAM::Policy CREATE_COMPLETE
|  Site site/ImageFunction/ServiceRole/DefaultPolicy AWS::IAM::Policy CREATE_COMPLETE
|  Site site/S3AssetUploader Custom::S3Uploader CREATE_COMPLETE
|  Site site/ImageFunction AWS::Lambda::Function CREATE_COMPLETE
|  Site site/ImageFunction/FunctionUrl AWS::Lambda::Url CREATE_COMPLETE
|  Site site/ServerFunction/ServerFunction/ServiceRole AWS::IAM::Role CREATE_COMPLETE
|  Site site/ImageFunction/LogRetention Custom::LogRetention CREATE_COMPLETE
|  Site site/ImageFunction AWS::Lambda::Permission CREATE_COMPLETE
|  Site site/RevalidationFunction/SqsEventSource:prodnextsstSitesiteRevalidationQueueFD225213 AWS::Lambda::EventSourceMapping CREATE_COMPLETE
|  Site site/ServerFunction/ServerFunction/ServiceRole/DefaultPolicy AWS::IAM::Policy CREATE_COMPLETE
|  Site site/ServerFunction/ServerFunction AWS::Lambda::Function CREATE_COMPLETE
|  Site site/ServerFunction/ServerFunction AWS::Lambda::Permission CREATE_COMPLETE
|  Site site/ServerFunction/ServerFunction/FunctionUrl AWS::Lambda::Url CREATE_COMPLETE
|  Site site/ServerFunction/ServerFunction/LogRetention Custom::LogRetention CREATE_COMPLETE
|  Site site/DisableLoggingPolicy AWS::IAM::Policy CREATE_COMPLETE
|  Site site/Distribution AWS::CloudFront::Distribution CREATE_COMPLETE
|  Site site/Parameter_url AWS::SSM::Parameter CREATE_COMPLETE
|  Site site/ServerFunctionInvalidatorPolicy AWS::IAM::Policy CREATE_COMPLETE
|  Site site/CloudFrontInvalidatorPolicy AWS::IAM::Policy CREATE_COMPLETE
|  Site site/CloudFrontInvalidator Custom::CloudFrontInvalidator CREATE_COMPLETE
|  Site AWS::CloudFormation::Stack CREATE_COMPLETE

✔  Deployed:
   Site
   SiteUrl: https://~~~~~~.cloudfront.net
```

---

### Remove from AWS

`npx sst remove --stage prod`

```bash
$ npx sst remove --stage prod
SST v2.38.4

➜ App: next-sst
Stage: prod
Region: ~~~~~~~~
Account: ~~~~~~~~

| Site public/Parameter_bucketName AWS::SSM::Parameter DELETE_COMPLETE
| Site AWS::Lambda::Permission DELETE_COMPLETE
| Site AWS::Lambda::Permission DELETE_COMPLETE
| Site AWS::IAM::Policy DELETE_COMPLETE
| Site site/Parameter_url AWS::SSM::Parameter DELETE_COMPLETE
| Site public/Bucket/Policy AWS::S3::BucketPolicy DELETE_COMPLETE
| Site AWS::IAM::Policy DELETE_COMPLETE
| Site Custom::CloudFrontInvalidator DELETE_COMPLETE
| Site Custom::LogRetention DELETE_COMPLETE
| Site site/ServerFunction/ServerFunction/LogRetention Custom::LogRetention DELETE_COMPLETE
| Site AWS::IAM::Policy DELETE_COMPLETE
| Site Custom::S3AutoDeleteObjects DELETE_COMPLETE
| Site Custom::S3Uploader DELETE_COMPLETE
| Site AWS::IAM::Policy DELETE_COMPLETE
| Site AWS::S3::BucketPolicy DELETE_COMPLETE
| Site LogRetentionaae0aa3c5b4d4f87b02d85b201efdd8a AWS::Lambda::Function DELETE_COMPLETE
| Site Custom::S3AutoDeleteObjectsCustomResourceProvider AWS::Lambda::Function DELETE_COMPLETE
| Site LogRetentionaae0aa3c5b4d4f87b02d85b201efdd8a/ServiceRole/DefaultPolicy AWS::IAM::Policy DELETE_COMPLETE
| Site AWS::Lambda::EventSourceMapping DELETE_COMPLETE
| Site AWS::Lambda::Function DELETE_COMPLETE
| Site Custom::S3AutoDeleteObjectsCustomResourceProvider AWS::IAM::Role DELETE_COMPLETE
| Site LogRetentionaae0aa3c5b4d4f87b02d85b201efdd8a/ServiceRole AWS::IAM::Role DELETE_COMPLETE
| Site AWS::IAM::Policy DELETE_COMPLETE
| Site AWS::IAM::Role DELETE_COMPLETE
| Site AWS::CloudFront::Distribution DELETE_COMPLETE
| Site AWS::Lambda::Url DELETE_COMPLETE
| Site AWS::Lambda::Url DELETE_COMPLETE
| Site AWS::CloudFront::CachePolicy DELETE_COMPLETE
| Site AWS::CloudFront::CloudFrontOriginAccessIdentity DELETE_COMPLETE
| Site AWS::CloudFront::Function DELETE_COMPLETE
| Site AWS::Lambda::Function DELETE_COMPLETE
| Site site/ServerFunction/ServerFunction AWS::Lambda::Function DELETE_COMPLETE
| Site AWS::IAM::Policy DELETE_COMPLETE
| Site AWS::IAM::Policy DELETE_COMPLETE
| Site AWS::DynamoDB::GlobalTable DELETE_COMPLETE
| Site AWS::IAM::Role DELETE_COMPLETE
| Site AWS::IAM::Role DELETE_COMPLETE
| Site site/ServerFunction/AssetReplacer Custom::AssetReplacer DELETE_COMPLETE
| Site site/ServerFunction/AssetReplacerPolicy AWS::IAM::Policy DELETE_COMPLETE
| Site AWS::S3::Bucket DELETE_COMPLETE
| Site CustomResourceHandler AWS::Lambda::Function DELETE_COMPLETE
| Site CustomResourceHandler/ServiceRole AWS::IAM::Role DELETE_COMPLETE
| Site AWS::SQS::Queue DELETE_COMPLETE

✔ Removed:
Site
```
