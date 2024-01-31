# deploy.yml file setup and meaning of the following statements:

name: Build image and trigger superrepo update

on:
  push:
    branches: 
        - 'main'

jobs:
  build:
     runs-on: ubuntu-latest  
     strategy:
         matrix:
             node-version: [18.x]

     steps:
       - uses: actions/checkout@v3
       - name: Use Node.js ${{ matrix.node-version }}
         uses: actions/setup-node@v3
         with:
           node-version: ${{ matrix.node-version }}
       - run: yarn install
       - run: yarn build
       - name: Configure AWS credentials
         uses: aws-actions/configure-aws-credentials@v2
         with:
           aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
           aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
           aws-region: ${{ secrets.AWS_DEFAULT_REGION }}
       - name: Remove old files from S3
         run: aws s3 rm ${{vars.AWS_S3_LOCATION_PROD}} --recursive
       - name: Upload to S3
         run: aws s3 cp dist/ ${{vars.AWS_S3_LOCATION_PROD}} --recursive
       - name: Invalidate CloudFront cache
         run: aws cloudfront create-invalidation --distribution-id ${{vars.AWS_CLOUDFRONT_DISTRIBUTION_ID}} --paths "/*"
