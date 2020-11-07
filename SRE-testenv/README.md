# SRE
SRE test-env

# Objective
This is a docker compose configuration which will run the following containers.

*Prometheus
*Alertmanager
*NGINX

# Overview
-    Set up Prometheus with alert manager + grafana first with node exporter first. 
-    Install Cadvisor
-    Set up an nginx container and and install node exporter tools on it 
-    Connnect the nginx container with prometheus
-    Verify that we can send alerts from the nginx container to prometheus

# Set up Prometheus with alert manager + node exporter
-    Prometheus as a Docker service although it can be installed as a binary or built from source.
-    Prometheus Node Exporter which exposes the metrics of the host machine
-    Grafana the front end for Prometheus
-    Prometheus alertmanager which will generate alerts based on the nginx ingress controller
-    create docker-compose.yml + prometheus.yml
-    Now point your browser to http://Target-IP-adress:9090 to confirm that server is running and click ‘Status’ to check the configuration file that we have provided.
-    The raw metrics can be checked by visiting http://Target-IP-adress:9090/metrics 
-    Next click the target option from the status menu, you will find that prometheus state is UP.

# Install Node Exporter
-    append docker-compose.yml to include the image and port details
     
# Restart docker-composer
root@demohost:~#  docker-compose restart

-    check node exporter state is UP
-    click 'Graph' from the menu-bar and then select any metrics from the drop down box and click 'Execute'. Press 'Graph' tab to view the graph

# Install Grafana
-    append the docker-compose.yml with the grafana image and port details for the Grafana dashboard set up.
-    point your browser to http://Prometheus-IP:3000 to access grafana. Login to grafana with the user 'admin' and password as 'admin_password'. You will be taken to grafana dashboard.
-    reference (https://linoxide.com/containers/setup-monitoring-docker-containers-prometheus/) for dashboard UI settings.

# Persist data to 2 docker volumes
append the following two lines in your docker-compose.yml and make sure both volumes are created and available on the local machine.

volumes:
./prometheus_db:/var/lib/prometheus
...............

...............
./grafana_db:/var/lib/grafana

# Alert and notification config
To setup notification, we need to configure three files-
-    Alert.rules to define rules on which alert will be fired
-    Map this file with the container in docker-compose.yml
-    Edit Prometheus.yml to add alertmanager as a service.
-    create alert.rules file containing (service_down) + (high_load) parameters
-    map the alert rules in docker-compose.yml
-    restart the containers (docker-compose restart)

# test alerting 
-    trigger (high_load) with load average greater than 0.5 
-    root@demohost:~#  docker run --rm -it busybox sh -c "while true; do :; done"
-    check http://localhostip:9090/alerts and you will see the alert high_load is active
-    click add row->click hamburger menu from the left->Add panel->graph. Select panel data source as prometheus>place mouse cursor in the metric lookup box>you will find                                ALERT at the top of the metrics.
-    Choose 'prometheus' from  drop-box and check 'alerts' from metric lookup
-    High load alerts is now visible in grafana dashboard

# Configure Alert receiver
-    Login to your hipchat account->select the room->integrations  Now click “Build your own integration's
-    Give a name to the integration and click create.
-    Note down room no and authentication token that we will use in configuring alertmanager.conf

# Create alertmanager.yml file
-    containing the parameters configured to send alerts to a hipchat account.
-    Once an alert is triggered by prometheus, the alert-manager will send the alert to the above hipchat account. You can verify it by navigating to the chat history.

# cAdvisor install
-    append cadvisor agent config to the docker-compose.yml
-    entry of cAdvisor service to prometheus.yml
-    root@demohost:~# docker-compose restart
-    Point your browser to http://target-IP:9090/targets to check the status of cAdvisor. If there is no error its state will be UP.






