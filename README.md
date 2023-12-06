For example, when service A calls B, and B calls C, which service has a problem? In Spring Cloud GCP, you can easily add distributed tracing by using Spring Cloud Sleuth. Spring Cloud Sleuth typically requires you to run and operate your own Zipkin backend.*

Cloud Trace is a distributed tracing system that collects latency data from your applications and displays it in the Google Cloud console. You can track how requests propagate through your application and receive detailed, near-real-time performance insights. Cloud Trace automatically analyzes all traces of your application to generate in-depth latency reports to surface performance degradations. It can also capture traces from all of your VMs, containers, or App Engine projects.

Cloud Trace API can be used to submit and retrieve trace data from any source. A Zipkin collector is also available, which enables Zipkin tracers to submit data to Cloud Trace. Projects running on App Engine are automatically captured.



student-02-15c544534d23@qwiklabs.net
viYMwbn5S5Fw
qwiklabs-gcp-01-b76bda664821


Task 1. Fetch the application source files:

	In Cloud Shell, enter the following command to create an environment variable that contains the project ID for this lab:
	   export PROJECT_ID=$(gcloud config list --format 'value(core.project)')
	   
	Verify that the demo application files were created:
	gcloud storage ls gs://$PROJECT_ID
	   
	Verify that the demo application files were created:
	gcloud storage ls gs://$PROJECT_ID

	Copy the application folders to Cloud Shell:
	gcloud storage cp -r gs://$PROJECT_ID/* ~/

	Make the Maven wrapper scripts executable:
	chmod +x ~/guestbook-frontend/mvnw
	chmod +x ~/guestbook-service/mvnw
	
Task 2. Enable Cloud Trace API:
     In this task, you enable the Cloud Trace API:
     	gcloud services enable cloudtrace.googleapis.com

Task 3. Add the Spring Cloud GCP Trace starter:

	open ~/guestbook-service/pom.xml.:
	Insert the following new dependency at the end of the dependencies section, just before the closing </dependencies> tag:
		<dependency>
		        <groupId>org.springframework.cloud</groupId>
		        <artifactId>spring-cloud-gcp-starter-trace</artifactId>
		</dependency>

	open ~/guestbook-frontend/pom.xml.:
	Insert the following new dependency at the end of the dependencies section, just before the closing </dependencies> tag:
		<dependency>
		        <groupId>org.springframework.cloud</groupId>
		        <artifactId>spring-cloud-gcp-starter-trace</artifactId>
		</dependency>
		
		
Task 4. Configure applications:

	Disable trace for testing purposes:
	For testing purposes, you disable trace in the application.properties files used for the local profile.:

	In the Cloud Shell code editor, open the application properties:
	guestbook-service/src/main/resources/application.properties
	
	Add the following property to disable tracing in the guestbook service:
	spring.cloud.gcp.trace.enabled=false
	
	In the Cloud Shell code editor, open the following:
	guestbook-frontend/src/main/resources/application.properties
	
	Add the following property to disable tracing in the guestbook frontend application:
	spring.cloud.gcp.trace.enabled=false
	
	
	Enable trace sampling for the cloud profile for the guestbook backend:
	For the cloud profile for the guestbook backend, you enable trace sampling for all requests in the application.properties file used for the cloud profile:

	In the Cloud Shell code editor, open the guestbook service cloud profile:
	guestbook-service/src/main/resources/application-cloud.properties
	
	Add the following properties to enable the tracing detail needed in the guestbook service:
	spring.cloud.gcp.trace.enabled=true
	spring.sleuth.sampler.probability=1.0
	spring.sleuth.scheduled.enabled=false
	
	Enable trace sampling for the cloud profile for the guestbook frontend:
	For the cloud profile for the frontend application, you enable trace sampling for all of the requests in the application.properties file used for the cloud profile.:

	In the Cloud Shell code editor, create a properties file for the guestbook frontend application cloud profile:
	guestbook-frontend/src/main/resources/application-cloud.properties
	
	Add the following properties to enable the tracing detail needed in the guestbook frontend application:
	spring.cloud.gcp.trace.enabled=true
	spring.sleuth.sampler.probability=1.0
	spring.sleuth.scheduled.enabled=false
	
Task 5. Set up a service account:

     gcloud iam service-accounts create guestbook
     
     Add the Editor role for your project to this service account:
	export PROJECT_ID=$(gcloud config list --format 'value(core.project)')
	gcloud projects add-iam-policy-binding ${PROJECT_ID} \
	--member serviceAccount:guestbook@${PROJECT_ID}.iam.gserviceaccount.com \
	--role roles/editor
	
      Generate the JSON key file to be used by the application to identify itself using the service account:
      This command creates Service Account Credentials that are stored in the $HOME/service-account.json file.:
      
      gcloud iam service-accounts keys create \
    ~/service-account.json \
    --iam-account guestbook@${PROJECT_ID}.iam.gserviceaccount.com
    
    
 Task 6. Run the application locally with your service account:
 
	Change to the guestbook-service directory:
	cd ~/guestbook-service
	
	Start the guestbook backend service application:
	./mvnw spring-boot:run \
	  -Dspring-boot.run.jvmArguments="-Dspring.profiles.active=cloud \
	  -Dspring.cloud.gcp.credentials.location=file:///$HOME/service-account.json"	
	
	Open a new Cloud Shell tab by clicking the Add icon to the right of the title of the current Cloud Shell tab.

	Change to the guestbook-frontend directory:

	cd ~/guestbook-frontend
	
	Start the guestbook frontend application:
	./mvnw spring-boot:run \
	  -Dspring-boot.run.jvmArguments="-Dspring.profiles.active=cloud \
	  -Dspring.cloud.gcp.credentials.location=file:///$HOME/service-account.json

Task 7. Examine the trace:	  
	  
         Open the Google Cloud console browser tab.:

	In the Navigation Menu, navigate to Trace > Trace Explorer in the Operations section, and then hit the Close button (X).

	At the top of the window, set the time range to 1 hour.:

	Turn on Auto reload. New trace data may take up to 30 seconds to appear.
	  
	  
	  
	  
	  
	  
	  
	  
	


