## Template for setting up a new deployable project at GCP (Flask at GAE)

#### notes:
- 'natality' is project name. replace it with a new name.
- Modify main.py, home.html and predict.html to suit your current project. It should be easy to figure out what to modify as long as the model has few predictors.
- For small projects, deployment according to this guide should take around 1 hour w/o accounting for editing those 3 files. This assumes you already have modeling notebook and evth goes smoothly. In any case, for simple projects it should not take more than 2 hours.
- This template developed based on this series of posts:
https://medium.com/@nutanbhogendrasharma/deploy-machine-learning-model-in-google-cloud-platform-using-flask-part-3-20db0037bdf8



## General workflow:
First, create an empty GH repo named like 'pg_natality'. Then clone it to project_repos folder. Build a model there, name it like nat_model.ipynb. Put model artifact into /<project>-app/ folder.
    
    
## To make deployment, follow this guide:

terminal:
    
    cd natality-app
    pip install virtualenv
    virtualenv natality-app-venv
    source natality-app-venv/bin/activate
    pip install Flask
    nano main.py
    
main.py:    
    
    #import Flask 
    from flask import Flask
    #create an instance of Flask
    app = Flask(__name__)
    @app.route('/')
    def home():
        return "Hello World"
    if __name__ == '__main__':
        app.run(debug=True)

terminal:    
    
    python main.py
    
check whether it work in another terminal window via curl. Then interrupt serving.
    
terminal:
    
    mkdir templates
    cd templates
    nano home.html
    
home.html:   
    
    <!doctype html>
    <html>
      <head>
        <title> Predict Baby Weight </title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
        <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css">
        <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.5.1/jquery.min.js"></script>
        <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.4.1/js/bootstrap.min.js"></script>    
      </head>
      <body>
          <div class="container">
            <div class="row my-5 pl-3">
                <h1>Predict Baby Weight</h1>
            </div>
            <!-- Starts form section -->
            <div class="form-container ">
                <form class="form-horizontal" action = "/predict/" method="post">
                    <div class="form-group row">
                      <label class="control-label col-sm-2" for="is_male">Is male? (Enter 1 or 0):</label>
                      <div class="col-sm-4">
                        <input type="text" class="form-control" id="is_male" name="is_male">
                      </div>
                    </div>
                    <div class="form-group row">
                      <label class="control-label col-sm-2" for="mother_age">Mother's age:</label>
                      <div class="col-sm-4">          
                        <input type="text" class="form-control" id="mother_age" name="mother_age">
                      </div>
                    </div>
                    <div class="form-group row">
                      <label class="control-label col-sm-2" for="plurality">Plurality:</label>
                      <div class="col-sm-4">
                        <input type="text" class="form-control" id="plurality" name="plurality">
                      </div>
                    <div class="form-group row">
                      <label class="control-label col-sm-2" for="gestation_weeks">Gestation (in weeks):</label>
                      <div class="col-sm-4">
                        <input type="text" class="form-control" id="gestation_weeks" name="gestation_weeks">
                      </div>
                    </div>
                    <div class="form-group row"> 
                    <label class="control-label col-sm-2" for="">&nbsp;</label>                
                      <div class="col-sm-offset-2 col-sm-4">
                        <button type="submit" class="btn btn-primary">Predict</button>
                      </div>
                    </div>
                  </form>
                <!-- Ends form section -->
            </div>
        </div>
      </body>
    </html>

        
terminal:    
        
    cd ..
    nano main.py
        
main.py:        
        
    #import Flask 
    from flask import Flask, render_template
    #create an instance of Flask
    app = Flask(__name__)
    @app.route('/')
    def home():
        return render_template('home.html')
    if __name__ == '__main__':
        app.run(debug=True)
        
terminal:
        
    python main.py
        
To test http locally, can use lynx http://127.0.0.1:5000 -dump as an alternative to baseline curl. Lynx renders http well enough to figure out whether it works. Notice that lynx pings dynamically, so you do not have to run it repeatedly. Then stop serving.
        
terminal:        
        
    cd templates
    nano predict.html
        
predict.html:
        
    <!doctype html>
    <html>
      <head>
        <title> Prediction </title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
        <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css">
        <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.5.1/jquery.min.js"></script>
        <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.4.1/js/bootstrap.min.js"></script>    
      </head>
      <body>
          <div class="container">
            <div class="row my-5 pl-3">
                <h1>Prediction is {{ prediction }}</h1>
            </div>
        </div>
      </body>
    </html>
        
Make sure that the model artifact is in the root project folder.
        
terminal:
        
    pip install numpy
    pip install joblib
    pip install sklearn==0.0
    pip install xgboost
    pip install pandas
    pip install --upgrade google-cloud-storage
    pip install yfinance

    nano main.py
        
main.py:
               
    #import Flask 
    from flask import Flask, render_template, request
    #create an instance of Flask
    app = Flask(__name__)
    @app.route('/')
    def home():
        return render_template('home.html')
    @app.route('/predict/', methods=['GET','POST'])
    def predict():
        if request.method == "POST":
            #get form data
            tv = request.form.get('tv')
            radio = request.form.get('radio')
            newspaper = request.form.get('newspaper')
            #call preprocessDataAndPredict and pass inputs
            try:
                prediction = preprocessDataAndPredict(tv, radio, newspaper)
                #pass prediction to template
                return render_template('predict.html', prediction = prediction)
            except ValueError:
                return "Please Enter valid values"
            pass        
        pass
    def preprocessDataAndPredict(tv, radio, newspaper):
        test_data = [tv, radio, newspaper]
        print(test_data)
        test_data = np.array(test_data).astype(np.float) 
        test_data = test_data.reshape(1,-1)
        print(test_data)
        file = open("lr_model.pkl","rb")
        trained_model = joblib.load(file)
        prediction = trained_model.predict(test_data)
        return prediction
        pass
    if __name__ == '__main__':
        app.run(debug=True)
        
(run locally, make sure it works)
        
[in app folder] pip freeze > requirements.txt
        
(you need 10-15 packages in requirements. remove some if there are too many)

terminal:
        
    nano app.yaml
      
app.yaml:
        
    runtime: python38
    service: "<project-name>"
     
terminal:        
        
    gcloud init
    gcloud app deploy





Debugging notes:
 - Can use Cloud Shell for debugging. So far this is the only way I know to run webapp interactively in GCP.
 - When testing deployment you may need to wait for a few minutes if you see "Internal Server Error".
 - If app works locally, but not on GAE, make sure that requirements.txt contains all dependencies.
 - GAE console > services has logs for each service. this decreases need to ever use cloud shell.
 - F1 instance of gae may have inusfficient ram for some use cases. You can pick more powerful instance by editing app.yaml.
 - You can put my own files into gae app folder. This will work as long as their path is specified relative to that folder.


