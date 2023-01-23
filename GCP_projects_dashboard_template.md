## This is a file explaining how to build dashboards in GCP

I will try this for a template:
https://medium.com/@andi_91543/building-a-data-analytics-dashboard-on-gcp-part-i-6249c42592e
Model monitoring in Vertex: https://medium.com/google-cloud/monitoring-ml-models-with-vertex-ai-5865131de353

Data studio seems to be the most common free tool to use to build dash board. Its drawback is that it does not have built-in alerting system. So I will have to build alerts separately. To do this, use custom metric, pulled from log files of Cloud function for model-eval. Modify model-eval CF to print out main performance metric so that I can see that in logs.

The easiest way to do model monitoring seems to do evth within Vertex. This could work if I sacrifice webapp serving. Or maybe I will later do both, i.e., will deploy model both via Vertex AI endpoint and GAE.
In general, I am still hesitant to go Vertex AI path because it seems not that mature, it will be harder to find info online and I will lose a lot of flexibility. Plus I have some time now to learn both DS/ML and SWE. Vertex AI can abstract away SWE so much that I will not learn anything there…

- built a dashboard relying on this guide: https://medium.com/@andi_91543/building-a-data-analytics-dashboard-on-gcp-part-i-6249c42592e
  Bought domain name from Google Domains (pmykola-mlprojects.com). Use www.<domain_name> as a name of a bucket to make it work.



    <iframe width="600" height="450" src="https://datastudio.google.com/embed/reporting/bfd5439a-4201-4657-b281-8edc7915b9c3/page/XNt9C" frameborder="0" style="border:0" allowfullscreen></iframe>


My domain (1 yr):
pmykola-mlprojects.com

https://domains.google.com/registrar/pmykola-mlprojects.com?authuser=0&hl=en-US

maybe see this: https://codelabs.developers.google.com/codelabs/cloud-webapp-hosting-gcs#0
short video on how to work in Data Studio to edit dashboard
