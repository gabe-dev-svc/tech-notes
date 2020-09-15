# CSA Exam - Serverless

## Lambda
- Lambdas can be invoked by API Gateway, AWS SDK, Amazon S3, Amazon Dynamo DB, or another Lambda function
- Lambda functions run seperately. That is, if I hit the same endpoint a million times triggering a Lambda function, a million Lambda functions will run 
- Lambda scales out (not up) automatically
- Pricing
  - First 1 million requests are free. $0.20 per million requests thereafter
  - Rounded up to nearest 100ms. Charged $0.0000167 per GB seconds used
- Architectures can get extremely complicated, AWS X-ray allows you to debug what's happening
- Can do things globally
- 