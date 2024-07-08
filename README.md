# MLflow local server



## Mlflow infrastructure

In this step we try to simulate on how MLflow being utilized in production by using docker containers. In our [docker-compose.yaml](docker-compose.yaml) file we create several services:

minio : this service will create s3 like storage service, which can be useful since usually we will use storage like AWS S3 or Google Storage.
pgsql : this service act as database storage used for MLflow backend store
mlflow-web : this service will act as MLflow Tracking UI to help us manage our experiment

Dockerfile of [mlflow server](mlflow_infrastructure/Dockerfile.mlflow)

## Running Mlflow servise

Before starting our experiment we need to start our services by running command

`docker-compose up -d`

This will start all our services, after all service started there is several things to do.
First, we need to create our bucket storage by visiting http://localhost:9000 , use credential defined on docker-compose.yaml to login. Create bucket with name mlflow.

![](https://github.com/ivangolt/mlflow_server/blob/main/docs/minio_bucket.png)

Access MLflow UI by visiting http://localhost:5000

## Running Mlflow experiments

To run mlflow experiments on local computer we need to define os enviroments variables

`os.environ["AWS_ACCESS_KEY_ID"] = "mlflow"`

`os.environ["AWS_SECRET_ACCESS_KEY"] = "password"`

`os.environ["MLFLOW_S3_ENDPOINT_URL"] = "http://127.0.0.1:9000"`


Also we need to deifne tracking uri and path of local dataset:

`MLFLOW_TRACKING_URI = "http://localhost:5000"`

`DATASET_PATH = "../data/mlflow_data/toxic_comments.csv"`

Then we set tracking uri and name of experiment:

`mlflow.set_tracking_uri(MLFLOW_TRACKING_URI)`

`embeddings_experiment = mlflow.set_experiment("TF_IDF")`

Then we start mlflow run:

`run_name = "logistic_regression"`

`with mlflow.start_run(run_name=run_name) as run:`
`...`
In mlflow we have two running 1) catboost and 2) logistic regression:

![](https://github.com/ivangolt/mlflow_server/blob/main/docs/mlflow_experiments.png)

There we can compare metrics of models

![](https://github.com/ivangolt/mlflow_server/blob/main/docs/compare_models.png)

After running experimnets model and metrics artifacts upload to s3 storage

![](https://github.com/ivangolt/mlflow_server/blob/main/docs/mlflow_minio.png)


Full code of task in [mlflow.ipynb](../code/mlflow.ipynb)
