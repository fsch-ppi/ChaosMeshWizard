### 🎛️Chaos Mesh Wizard"

This repository provides a framework for quickly deploying chaos tests using Chaos Mesh to any Kubernetes enviroment.
Chaos Mesh Wizard provides capabilities to create yaml the easy way - by templating it in Json. Additionally tests can be run, monitored and the data can be analyzed.

It originated in the Seminars work in my University.

I have an example benchmark of a streaming processing algorithm in another repository. You can find it [here](https://github.com/fsch-ppi/HMLC-benchmark). This can also help you if you have never configured a kubernetes environment before

#### 🛠️ Setup
Install Chaos Mesh
```shell
helm repo add chaos-mesh https://charts.chaos-mesh.org
kubectl create ns chaos-mesh
helm install chaos-mesh chaos-mesh/chaos-mesh -n=chaos-mesh --version 2.7.0
```
Additionally if you want to use the dashboard without generating a RBaC key for yourself, you can disable permission authentification.

**NOTE**: If others have access to the same machine, or the Chaos Mesh Dashboard will be exposed to the internet, this is a bad idea.
```shell
# Disable permission authentification
helm upgrade chaos-mesh chaos-mesh/chaos-mesh --namespace=chaos-mesh --version 2.7.0 --set dashboard.securityMode=false
```

Finally, install the python requirments
```shell
pip install -r requirements.txt
```

Now you are ready to go - well almost.

Make sure you have an applicaiton running in a Kubernetes environment, that you want to monitor.
Adittionally you will need a REST service or similar, which you have to fetch data from using the get_logs() function.
This is currently defined for a Prometheus service - if you want to use something else, change [/chaos_lib_utils/prometheus_utils.py](/chaos_lib_utils/prometheus_utils.py)

#### 🔍 Defining Chaos Tests
You can define chaos tests in two main ways:
1. Use the Chaos Dashboard (UI of Chaos mesh), download YAML and paste it in the [/experiments](/experiments) folder.

2. Create a Jsonnet template in [/experiments/jsonnet_templates](/experiments/jsonnet_templates/).
Examples can be found [here](/experiments/unused_jsonnet_templates/_explanation_workflows.jsonnet) for workflows and [here](/experiments/unused_jsonnet_templates/_explanation_singletests.jsonnet) for single experiments.

When you run the main [CLI application](/chaos_wizard_cli.py), Jsonnet templates will be automatically converted to valid yaml. 

The [helpers](/experiments/jsonnet_templates/helpers/) folder holds all basic definitions for the currently supported tests. As of now this are only NetworkChaos, PodChaos and TimeChaos [refer to](https://chaos-mesh.org/docs/simulate-pod-chaos-on-kubernetes/)

#### 🧪 Running single tests
Before running your first test, take a look into [config.env](/config.env). This holds test parameters, such as the interval in which the logging service fetches data and how long you want runs to be. Make sure this somewhat alligns with your Chaos Test definitions.


To run tests, start the main application with 
```shell
python3 chaos_wizard_cli.py
```
Select the tests you want to run.


#### 📊 Data Analysis
The modular architecture provides a lot of predefined functions, especially for detecting and plotting pod failures. The data analysis part can be found in [/chaos_lib_utils/reporting.py](/chaos_lib_utils/reporting.py)