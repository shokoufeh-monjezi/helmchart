---


---

<h1 id="triton-inference-server-helm-chart">Triton Inference Server Helm Chart</h1>
<p>The steps below describe how to set-up a model repository, use helm to launch the inference server, and then send inference requests to the running server.</p>
<h2 id="running-the-server">Running the Server:</h2>
<p>Run the following commands or check the “Quick start guide” in this address:</p>
<p><a href="https://github.com/triton-inference-server/server/blob/master/docs/quickstart.md">https://github.com/triton-inference-server/server/blob/master/docs/quickstart.md</a> to start the server.</p>
<ol>
<li>Clone the triton inference server using following command:<br>
Git clone <a href="https://github.com/triton-inference-server/server.git">https://github.com/triton-inference-server/server.git</a></li>
<li>Move to the examples folder:</li>
</ol>
<pre><code>$ cd server/docs/examples
</code></pre>
<p><img src="https://lh4.googleusercontent.com/_sHuxWfa_Zw5BOOIyevkWeSVz3YMjWopMTyeYZB3r-nGjKau6jZMLWfGYjPQKmvOqSrVjEuECla1s7FAjrCoSkxXkSMXCKJaNuPQ2SxGLx2aWG3vofmKhxfrRAaFsH7OCz6nxY_g" alt=""></p>
<p>The model repository is the directory where you place the models that you want. An example model repository is included in the docs/examples/model_repository. Before using the repository, you must fetch any missing model definition files from their public model zoos via the provided script.<br>
3. fetch missing models:</p>
<pre><code> $ ./fetch_models.sh
</code></pre>
<p><img src="https://lh6.googleusercontent.com/a0i3ws_9eNJFSR2GG6kYATGzavm4kDaOilNAdSKJSRw7EpMk_XVipM14o6_P1WuNcjTQxK3nLDVzT6R0-uEy5H9HDVYujlM3V0Fe_lQ68P8YvWWjby3dRESS7XTHGwjfd4m78V5Q" alt=""></p>
<p>In order to pull the triton server from aws registry, use your aws account access key and secret access key to configure your aws CLI options.<br>
4. aws configuration</p>
<pre><code> $ aws configure
AWS Access Key ID [None]: &lt;acess key&gt;
AWS Secret Access Key [None]:&lt;secret access key&gt;
Default region name [None]: us-east-2
Default output format [None]: json
</code></pre>
<p>After that, use aws ecr get-login-password to log in to aws registry. This command retrieves and displays an authentication token using the GetAuthorizationToken API that you can use to authenticate to an Amazon ECR registry. You can pass the authorization token to the login command of the container client of your preference, such as the Docker CLI. After you have authenticated to an Amazon ECR registry with this command, you can use the client to push and pull images from that registry as long as your IAM principal has access to do so until the token expires.<br>
5. aws ecr get login:</p>
<pre><code> $ aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 709825985650.dkr.ecr.us-east-1.amazonaws.com
</code></pre>
<p><img src="https://lh4.googleusercontent.com/Oh51ISo9gR44Q9bC_FMNAJT8VVBHPuhIRpsApV3KPzfhNe9d6LrjWjVTY9C_-ASHf1oIzv_6MgDaW-ax8XioY7WAzaWPYKMrV-d9SISHmlFXribERLdBlGQltiNe-s4BztGWajUO" alt=""></p>
<ol start="6">
<li>Pull the triton server using following command :</li>
</ol>
<pre><code>docker pull 709825985650.dkr.ecr.us-east-1.amazonaws.com/nvidia/containers/nvidia/tritonserver:20.11-py3
</code></pre>
<p><img src="https://lh6.googleusercontent.com/rBMSY6imUF_X8TPFvgZp4tOG-16wkVDdBt3-97Ve9YMeml9ky1qMNJkGPoAYmI_FlOoO3dbJTJGnuAXmxfx6WgsmL9P5iRcrLNsCkfWWsJG9uRQ_Wmrht-fKF9078A7pA98nRt0r" alt=""></p>
<ol start="7">
<li>Run the server:</li>
</ol>
<p>Triton is optimized to provide the best inferencing performance by using GPUs, but it can also work on CPU-only systems. In both cases you can use the same Triton Docker image.</p>
<p>Use the following command to run Triton with the example model repository you just created. The NVIDIA Container Toolkit must be installed for Docker to recognize the GPU(s). The --gpus=1 flag indicates that 1 system GPU should be made available to Triton for inferencing.<br>
<strong>Run on GPU:</strong></p>
<pre><code>$ docker run --gpus=1 --rm -p8000:8000 -p8001:8001 -p8002:8002 -v/full/path/to/docs/examples/model_repository:/models nvcr.io/nvidia/tritonserver:&lt;xx.yy&gt;-py3 tritonserver --model-repository=/models
</code></pre>
<p><strong>Run on the CPU:</strong></p>
<pre><code>$ docker run --rm -p8000:8000 -p8001:8001 -p8002:8002 -v/full/path/to/docs/examples/model_repository:/models nvcr.io/nvidia/tritonserver:&lt;xx.yy&gt;-py3 tritonserver --model-repository=/models
</code></pre>
<p><strong>Example</strong><br>
For the version of the triton server that we used in this document the command would be like this:</p>
<pre><code>$ docker run --rm --gpus=1 -p8000:8000 -p8001:8001 -p8002:8002 -v /home/ubuntu/server/docs/examples/model_repository:/models 709825985650.dkr.ecr.us-east-1.amazonaws.com/nvidia/containers/nvidia/tritonserver:20.11-py3 tritonserver --model-repository=/models
</code></pre>
<p><img src="https://lh4.googleusercontent.com/TcS7LZ5HSV5sGREdluufA6HpX2Y80D-c-wqXsMWRMwBVcJ2yarMldljPG9VT8FkblqERmaBJG1k62BbbDQp37DlZINk2FYvbdKmVj_4GQPkkfxS0LMdTrrDIjEFFS7__J0uqhepG" alt=""></p>
<p>After starting Triton you will see output on the console showing the server starting up and loading the model. When you see output like the following, Triton is ready to accept inference requests.</p>
<p><img src="https://lh4.googleusercontent.com/tyHl4xNOLgaxaqMSFH2mVpZVGEI0UNLEjJTRUx9onotbaWslDZJGMWBDXXrq2PI2jtQB1H4wNnZYHNbpQ04WthgFtaCUqMDbpGgzKoqjrr2s2DK459G8HjYFOnC82pZDgICwl235" alt=""></p>
<h2 id="send-inference-request-to-server">Send Inference Request to Server:</h2>
<p>Before starting to work with EKS and helm you need to install these four applications.<br>
<strong>AWS CLI</strong> – Command line tools for working with AWS services, including Amazon EKS.</p>
<p><strong>eksctl</strong> – A command line tool for working with EKS clusters that automates many individual tasks.</p>
<p><strong>kubectl</strong> – A command line tool for working with Kubernetes clusters.</p>
<p><strong>Helm</strong> _ Helm is a package manager for Kubernetes</p>
<p>Use <a href="https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html">https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html</a> to learn how to install them on linux, mac or windows systems.</p>
<p>In order to have access to aws registry and pull or push containers from this registry do the next two steps:</p>
<ol>
<li>change aws configuaration based on your account information using:</li>
</ol>
<pre><code>$ aws configure
</code></pre>
<ol start="2">
<li>Aws ecr get login:</li>
</ol>
<pre><code>$ aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 709825985650.dkr.ecr.us-east-1.amazonaws.com
</code></pre>
<p>Clone the triton-inference-server using following command:</p>
<pre><code>Git clone [https://gitlab-master.nvidia.com/asawarkar/aws-triton-vr.git](https://gitlab-master.nvidia.com/asawarkar/aws-triton-vr.git)
</code></pre>
<p>Triton Server needs a repository of models that it will make available for inferencing. For this example you will place the model repository in an Amazon S3 Storage bucket. Following is the command to make an s3 bucket.</p>
<pre><code>$ aws s3 mb &lt;target&gt; [--options]
</code></pre>
<p>Follow the steps in the first part of this document to download models in your system or run these three commands that we used in the first part to fetch models:</p>
<pre><code>$ Git clone https://github.com/triton-inference-server/server.git
$ cd server/docs/examples
$ ./fetch_models.sh
</code></pre>
<p>You can read more details about the process  in quick start part in this address: <a href="https://github.com/triton-inference-server/server/blob/master/docs/quickstart.md">https://github.com/triton-inference-server/server/blob/master/docs/quickstart.md</a> .</p>
<p>Following command shows how to copy models in the bucket:</p>
<pre><code>$ aws s3 cp docs/examples/model_repository s3://&lt;bucket name&gt;
</code></pre>
<p>Make sure the bucket permissions are set so that the inference server can access the model repository.<br>
In the next step make a cluster using the following command. You can change the node-type.</p>
<pre><code>$ eksctl create cluster --name triton-vr --version 1.18 --node-type=g4dn.xlarge --nodes=1
</code></pre>
<p><img src="https://lh3.googleusercontent.com/4sdcwsaJKpQfcoU3RGmr1RT8P_yh0h8x2ovwqNAUS1rRQ2c03C_-wxKyvqOMUib91HIiQV6JD5TQtPYPuEBFurAm5kLCbymj3NaE3st5lV-Cw1WV_yD0l7Hf1YJV0keeEK4eEWp3" alt=""><img src="https://lh3.googleusercontent.com/vkF7XUdVi2CzuhPwIWYoRXviH_TeX6gk8sYclND5ilx6PvyGWIN3Hw53Qjc7wsl-Ho0Kn6zxmfBCyBg0YcrzBXaVkAEXg32A4tp7OyyFGYgHrbA4MPVE5NZkG_5xt0p6KYGY1W0X" alt="Abhilash Somasamudramath US"></p>
<p>Run following command to see all available namespaces:</p>
<pre><code>$ kubectl get all --all-namespaces
</code></pre>
<p><img src="https://lh5.googleusercontent.com/P9zH9urXl5FWPQVFgva9Pnomb-RNFxfai-670LiFyS6FHyUqouAilUftNFHHPN5i6eoonx_aAPl_DAJ3co0Kds3kovs1_A2ChmMKx8XJF9yub62JoYPtCngrCVOogEn-1oIXgwbb" alt=""></p>
<h2 id="get-repo-info">Get Repo Info</h2>
<p>Installs the kube-prometheus stack, a collection of Kubernetes manifests, Grafana dashboards, and Prometheus rules combined with documentation and scripts to provide easy to operate end-to-end Kubernetes cluster monitoring with Prometheus using the Prometheus Operator.</p>
<pre><code>$ helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

$ helm repo add stable https://charts.helm.sh/stable

$ helm repo update
</code></pre>
<h3 id="install-helm-chart">Install helm chart:</h3>
<p>helm install [release-name] prometheus-community/kube-prometheus-stack</p>
<pre><code>$helm install stable prometheus-community/kube-prometheus-stack
</code></pre>
<p><img src="https://lh6.googleusercontent.com/uvEqm2SBJCxjCF30l-YE2AiCdp3RIW2WPSQlqvqmx22EeluObzMbPbJONTsI22FAIvhdULtbVdVwD1nX2q-8OvtqrIBKQysPzuqyjyfNRcgjOLJTbjpn0thY_-DpXd00hmzlQsTd" alt=""></p>
<p>Move to the triton folder that you clone and it has Chart.yaml and values.yaml files in it. In the templates folder inside triton you can see deployment.yaml file. Open this file and edit the container part of it by adding an environment variable section to it. The container section should be like this:</p>
<pre><code>containers:
	- name: {{ .Chart.Name }}
	image: "{{ .Values.image.imageName }}"
	imagePullPolicy: {{ .Values.image.pullPolicy }}
	env:
		- name: AWS_ACCESS_KEY_ID
		value: &lt;your access key&gt;
		- name: AWS_SECRET_ACCESS_KEY
		value: &lt; your secret-access-key&gt;
		- name: AWS_DEFAULT_REGION
		value: &lt;your-region&gt;
</code></pre>
<p>Change the env values based on your account information then back to the main triton folder to install triton:</p>
<pre><code>$ Cd &lt; folder of triton&gt;
$ helm install triton .
</code></pre>
<p><img src="https://lh4.googleusercontent.com/U32fhaWJpVKdrkKh1gQn7CyGWCNTmdiVt1MW81lMAMUfGNvKfbtz5dDUpm5q2rF5U4g69_CWJYPKi5B6vd2g6AKmIGr9NSDlGdeY9NYoc83Op3wK044qgc7W279tN73-eP81ZatG" alt=""></p>
<p>Use kubectl to see status and wait until the inference server pods are running.</p>
<pre><code>$ kubectl get pods
</code></pre>
<p><img src="https://lh6.googleusercontent.com/vVn81F1Wnc632NBcpzJtE07BLak9N7OLDjevlsp42J88dUVXm5IkVTANgvvo_9dY0VZTyH0X1MkhZmpO01twGwbM2wDhUg67qff2sKPtOGr9_2r4NTLIjv-whKSyXbYNtGBkFJwj" alt=""></p>
<p>Now that the inference server is running you can send HTTP or GRPC requests to it to perform inferencing. By default, the inferencing service is exposed with a LoadBalancer service type. Use the following to find the external IP for the inference server.</p>
<pre><code>$ kubectl get services.
</code></pre>
<p><img src="https://lh5.googleusercontent.com/0pAUQmnq09p4CthRjGLodwfI_QBOzIQFKEQ30ZpJ-4cyhUuY1xiwyGshDRGCpOBm77xc_zKnO03HU-X78jr-OaQ9x5tFXLTaIah2pSPMmW7HYjvEGtzPYLyGj_WWsXk9LIbeqOYl" alt=""></p>
<h3 id="getting-the-client-example">Getting the client example:</h3>
<p>Pull the triton container using following command:</p>
<pre><code>$ docker pull 709825985650.dkr.ecr.us-east-1.amazonaws.com/nvidia/containers/nvidia/tritonserver:20.11-py3-clientsdk
</code></pre>
<p><img src="https://lh3.googleusercontent.com/38_vKCvw4_Y8vAYuPVKGTSlNFW6cIOqNOsguJ-ZM9sblU-Jp9ASlyjICq-Cx9r_lEriumQlY6OCoRpgKT2wPEMoFGwgDHRhC0Mez_SuYV8Z5h2aVOU_x1XaZgplaYpb79X_W273H" alt=""></p>
<h3 id="run-the-container">Run the container:</h3>
<pre><code>$docker run -it --rm --net=host 709825985650.dkr.ecr.us-east-1.amazonaws.com/nvidia/containers/nvidia/tritonserver:20.11-py3-clientsdk
</code></pre>
<p>Inside the container move to bin folder:</p>
<pre><code>$ cd install/bin
</code></pre>
<p>Inside bin folder test image classification model with one image. This part of command “<a href="http://a2682a4190ad24af4924534ea9e433fc-1837992654.us-east-2.elb.amazonaws.com:8000">a2682a4190ad24af4924534ea9e433fc-1837992654.us-east-2.elb.amazonaws.com:8000</a>” is &lt;external ip address of load balancer: 8000&gt; so you need to change this ip address with yours.</p>
<pre><code>$ image_client -u a2682a4190ad24af4924534ea9e433fc-1837992654.us-east-2.elb.amazonaws.com:8000 -m densenet_onnx -c3 /workspace/images/mug.jpg
</code></pre>
<p><img src="https://lh3.googleusercontent.com/Rcz4YrOpLR7DbEjBKKPwb3PaEHuc6r7ZkaFTziUlDCOBiAPK9fAEMpNd4ry6hAZxrEocKa8BNhU6MJTEBhxDKyHjqjRc0Jyw32L7x2yFLRtHtZx3PwpQuqM-_tcTR4vL85EgIxuo" alt=""></p>
<h3 id="clean-up">Clean up</h3>
<p>Once you’ve finished using the inference server you should use helm to<br>
delete the deployment.</p>
<pre><code>$ helm list
$ helm delete &lt; helm name&gt;
</code></pre>
<p>You can delete the bucket too using this command:</p>
<pre><code>$ aws s3 rb &lt;target&gt; [--options]
</code></pre>

