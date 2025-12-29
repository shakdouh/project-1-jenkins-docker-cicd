pipeline {
  agent any

  environment {
    IMAGE_NAME     = "hesham-cicd-demo"
    CONTAINER_NAME = "cicd-demo"
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Unit Tests') {
      steps {
        sh '''
          set -e
          python3 -m venv venv
          . venv/bin/activate
          pip install --no-cache-dir -r app/requirements.txt pytest
          pytest -q
        '''
      }
    }

    stage('Build Docker Image') {
      steps {
        sh '''
          set -e
          docker build -t '"$IMAGE_NAME"':latest .
        '''
      }
    }

    stage('Run Container') {
      steps {
        sh '''
          set -e
          docker rm -f "$CONTAINER_NAME" 2>/dev/null || true
          docker run -d --name "$CONTAINER_NAME" -p 5000:5000 "$IMAGE_NAME":latest
          docker ps --filter "name=$CONTAINER_NAME"
        '''
      }
    }

    stage('Smoke Test (inside app container)') {
      steps {
        sh '''
          set -e
          # انتظر قليلًا حتى يبدأ Flask
          sleep 2

          # نفّذ طلب HTTP من داخل نفس حاوية التطبيق (فيها python جاهز)
          docker exec "$CONTAINER_NAME" python - <<'PY'
import urllib.request, time
url = "http://127.0.0.1:5000/"
for i in range(10):
    try:
        print(urllib.request.urlopen(url, timeout=2).read().decode())
        raise SystemExit(0)
    except Exception as e:
        time.sleep(1)
raise SystemExit("Smoke test failed: app not responding")
PY

          # (اختياري) اطبع آخر logs للتوثيق
          docker logs --tail 30 "$CONTAINER_NAME" || true
        '''
      }
    }

    stage('Manual verification hint') {
      steps {
        echo "Now the app container is kept running for manual curl and screenshots. Try: curl http://localhost:5000"
      }
    }
  }

  post {
    always {
      sh '''
        # لا نحذف الحاوية تلقائيًا لكي تقدر تعمل curl + screenshots
        echo "Cleanup is manual. When done run: docker rm -f '"$CONTAINER_NAME"'" 
      '''
    }
  }
}

