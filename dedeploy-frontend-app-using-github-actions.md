# deploy.yml file setup and meaning of the following statements:

    name: Build image and trigger superrepo update                           // give a name to your deployment
    
    on:
      push:
        branches: 
            - 'main'                                                        // this is the branch, where if anything get pushed then the deploy.yml file will trigger and automatic deployment process will start 
    
    jobs:
      build:                                                                // here we mention which os are we using, what is the version of it 
         runs-on: ubuntu-latest  
         strategy:                                                          // under strategy we mention the node version we want to use 
             matrix:
                 node-version: [18.x]

         steps:                                                             // here we mention the following steps which will run and it includes different actions and aws credintials version
           - uses: actions/checkout@v3                                      // use the github actions with checkout version 3
           - name: Use Node.js ${{ matrix.node-version }}                   // it pulls the matrix node version from the strategy and use that here
             uses: actions/setup-node@v3                                    // use the actions and setup the node with version 3
             with:
               node-version: ${{ matrix.node-version }}                     // it pulls the matrix node version from the strategy and use that here
           - run: yarn install                                              // install yarn to build the project
           - run: yarn build                                                // build the project
           - name: Configure AWS credentials                                // here we configure the aws creadintials
             uses: aws-actions/configure-aws-credentials@v2                 // uses aws-actions which configure aws credentials with version 2
             with:                                                          // add secrects to your github secrets
               aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}          // pulls the aws_access_key_id from github secrects
               aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}  // pulls aws_secret_access_key from github actions 
               aws-region: ${{ secrets.AWS_DEFAULT_REGION }}                // pulls the aws_default_region from github actions
           - name: Remove old files from S3                                 // will remove the old files from s3 bucket
            // add variables to github variables 
             run: aws s3 rm ${{vars.AWS_S3_LOCATION_PROD}} --recursive      // removes all old build files from aws s3 bucket with the help of bucket location which pulls location from github variables
           - name: Upload to S3                                             // upload new build files to aws s3
             run: aws s3 cp dist/ ${{vars.AWS_S3_LOCATION_PROD}} --recursive // copy and peast new build files to aws s3 bucket with the help of bucket location which pulls location from github variables
           - name: Invalidate CloudFront cache                              // need to invalidate cloudfront caches
            // add cloudfront_distribution_id in github variables
             run: aws cloudfront create-invalidation --distribution-id ${{vars.AWS_CLOUDFRONT_DISTRIBUTION_ID}} --paths "/*"     // this command creates the cloudfront invalidation " /* " means remove all caches

             
# Where to set secrets and variables
# Note : Please keep all importaint key and values as Secrets (Those things you do not want to share with anyone, i.e. sensitive data or information)
    1. Go to your reposotory
    2. Click on the settings tab
    3. There you find a Secrets and Variables folder
    4. Go to Actions option
    5. Create a new Repository Secret
    6. Give it a name (AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY, AWS_DEFAULT_REGION)and set the value of it - make sure you're using these mentioned names otherwise it' won't work
    7. Go to valiables tab 
    8. Create a new Repository Variable
    9. Give it a name (AWS_S3_LOCATION_PROD, AWS_CLOUDFRONT_DISTRIBUTION_ID)and set the value of it - make sure you're using these mentioned names otherwise it' won't work
    10. S3 bucket location : Make sure to add your s3 bucket location in this format (Ex: s3://<your s3 bucket name>)
    11. That's all you need to do, for deploy a node app front-end with the help of github actions
    12. Thank You!
![image](https://github.com/bayshore-intelligence-solution/DevOps-Docs/assets/143008309/ecff4277-6d9f-49f6-8d7b-8c963b841aae)
![image](https://github.com/bayshore-intelligence-solution/DevOps-Docs/assets/143008309/af1454dd-b20c-429d-8eca-f4fb03aa552d)
![image](https://github.com/bayshore-intelligence-solution/DevOps-Docs/assets/143008309/298a4432-a83f-4c02-8b58-5e78f251d57c)
![image](https://github.com/bayshore-intelligence-solution/DevOps-Docs/assets/143008309/fe76cca9-35c1-4e2c-b270-dc1a64dd4376)
![image](https://github.com/bayshore-intelligence-solution/DevOps-Docs/assets/143008309/88ee0e8d-4e21-4c8e-831d-532f78b8fd24)
