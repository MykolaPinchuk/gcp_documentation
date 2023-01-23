## Template for setting up a periodic Cloud Function run at GCP 

notes:
main source:
https://medium.com/mlearning-ai/scheduling-google-cloud-functions-to-run-periodically-4fd3e763e78f
choose 'allow unauthenticated'

- an issue is how to save csv files to the bucker via cloudFunction
so far i keep getting error 'could not handle request'
the trick was to upload csv to the bucket properly. see code in project_repos and GCP CF.
- gsutil does not work on Cloud Functions. The only way is to use cloud SDK (i.e., blobs) to implement 'ls' functionality
- debugging Cloud Function is not easy. so far, I use 3 options:
-- console CF details tab - if function fails to deploy
-- console CF logs - if functions either fails to deploy or does not work
-- cloudshell 'gcloud functions logs read <cf name>, usually the same as option 2.
if i will wrok with CFs a lot, it will make sense to invest in debugging tools like https://towardsdatascience.com/how-to-develop-and-test-your-google-cloud-function-locally-96a970da456f

Cloud scheduler part is trivial. Before playing with cloud scheduler, make sure CF works via testing. CF must not require auth for schduler to easily hook up to it.


