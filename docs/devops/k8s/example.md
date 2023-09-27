## multiple containers in single pod
```yaml
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    spring-boot: "true"
    app: multiple-pod-test
    provider: jkube
    version: 1.0.5-SNAPSHOT
    group: com.metaleap.framework
  name: multiple-pod-test
spec:
  replicas: 1
  revisionHistoryLimit: 2
  selector:
    matchLabels:
      app: multiple-pod-test
      provider: jkube
      group: com.metaleap.framework
  strategy:
    rollingUpdate:
      maxSurge: 5
      maxUnavailable: 0
    type: RollingUpdate
  template:
    metadata:
      labels:
        spring-boot: "true"
        app: multiple-pod-test
        provider: jkube
        version: 1.0.5-SNAPSHOT
        group: com.metaleap.framework
    spec:
      containers:
      - env:
          - name: sqlhostname
            value: sql-test-pod
          - name: HOSTNAME
            valueFrom:
              fieldRef:
                fieldPath: metadata.name
        image: nginx
        ports:
          - containerPort: 80
            name: http
            protocol: TCP
        imagePullPolicy: IfNotPresent
        name: python-utils
        livenessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 20
          periodSeconds: 9
          timeoutSeconds: 10
        readinessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 25
          periodSeconds: 9
          timeoutSeconds: 10


      - env:
        - name: SPRING_PROFILES_ACTIVE
          valueFrom:
            configMapKeyRef:
              name: base
              key: SPRING_PROFILES_ACTIVE
        - name: APPLICATION_NAME
          value: ${project.artifactId}
        - name: TZ
          value: Asia/Shanghai
        - name: KUBERNETES_NAMESPACE
          valueFrom:
            fieldRef:
              fieldPath: metadata.namespace
        - name: HOSTNAME
          valueFrom:
            fieldRef:
              fieldPath: metadata.name
        image: gitlab.pixdaddy.com:5050/magic/backend/magic-brush/magic-brush-app:1.0.1-SNAPSHOT
        imagePullPolicy: IfNotPresent
        lifecycle:
          postStart:
            exec:
              command:
              - sh
              - -c
              - |
                while ! netstat -tuln | grep ":80 "; do
                  echo 'check port use netstat';
                  sleep 5;  # 等待 5 秒后再次检查
                done
        name: my-sqldb
        ports:
        - containerPort: 8090
          name: http
          protocol: TCP
        securityContext:
          privileged: false
      dnsConfig:
        options:
        - name: single-request-reopen
        - name: ndots
          value: "2"
      imagePullSecrets:
      - name: registry-key
```