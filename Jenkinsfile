pipeline {
    agent any
    
      environment {
        
        JAVA_HOME = 'C:/Program Files/Java/jdk-17'
    }

    stages {
        
       

        stage('Start Prometheus') {
            steps {
                bat 'docker run -d -p 9092:9092 --name prometheus prom/prometheus'
            }
        }

        stage('Create Grafana Dashboard') {
            environment {
                PROMETHEUS_PORT = 9090
                API_KEY = 'eyJrIjoiTXB5Z01md0Y2dWtrNEhlUE1jMmQ2N2JXV0RTQ1NVZjMiLCJuIjoibWFuYXMiLCJpZCI6MX0='
            }
            steps {
                bat 'docker run -d -p 3131:3001 --name grafana grafana/grafana'
//                 bat 'timeout /t 10 /nobreak'
                bat "curl -X POST -H \"Content-Type: application/json\" \
                    -d '{\"name\":\"db\",\"type\":\"prometheus\",\"url\":\"http://192.168.234.1:9090\",\"access\":\"proxy\",\"isDefault\":true}' \
                    http://admin:${API_KEY}@192.168.234.1:3000/api/datasources"
                bat "curl -X POST -H \"Content-Type: application/json\" \
                    -d '{\"dashboard\":{\"id\":null,\"title\":\"${JOB_NAME}-${BUILD_NUMBER}\",\"tags\":[\"devops\"],\"timezone\":\"browser\",\"schemaVersion\":21,\"panels\":[{\"id\":1,\"gridPos\":{\"x\":0,\"y\":0,\"w\":12,\"h\":8},\"type\":\"graph\",\"title\":\"Panel Title\",\"datasource\":\"db\",\"targets\":[{\"expr\":\"up\",\"legendFormat\":\"\",\"refId\":\"A\"}],\"xaxis\":{\"mode\":\"time\",\"show\":true},\"yaxes\":[{\"format\":\"short\",\"show\":true},{\"format\":\"short\",\"show\":true}]},{\"collapsed\":false,\"gridPos\":{\"h\":2,\"w\":24,\"x\":0,\"y\":8},\"id\":2,\"panels\":[],\"title\":\"\",\"type\":\"row\"}],\"version\":0,\"links\":[]},\"overwrite\":false}' \
                    http://admin:${API_KEY}@192.168.234.1:3000/api/dashboards/db"
            }
        }
        stage('Build') {
            steps {
                bat 'docker-compose build'
            }
        }
        
        stage('Deploy') {
            steps {
                bat 'docker-compose up -d'
            }
        }
    }



}
