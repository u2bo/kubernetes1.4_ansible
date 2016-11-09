
kubernetes1.4_ansible
============


使用ansible一键安装k8s集群，集群除kubelet外全部容器化相关组件：
+   集群使用的k8s版本为1.4.1
+   默认安装使用supervisor保证kubelet的可用性
+   使用kubelet的pod-manifest-path保证core组件的可用性
+   网络暂使用flannel(容器化) vxlan的模式也是由kubelet保证其可用性
+   集群安装默认开启https及token验证
+   集群默认安装dns及1.4的dashboard


Sample tree forder
------------
    ├── hosts
    ├── roles
    │   ├── swqk8smaster
    │   │   ├── defaults
    │   │   │   └── main.yml
    │   │   ├── files
    │   │   │   ├── cni
    │   │   │   │   └── bin
    │   │   │   ├── images
    │   │   │   │   ├── etcd-amd64.tar
    │   │   │   │   ├── exechealthz-amd64.tar
    │   │   │   │   ├── flannel.tar
    │   │   │   │   ├── kube-agent.tar
    │   │   │   │   ├── kube-apiserver.tar
    │   │   │   │   ├── kube-controller-manager.tar
    │   │   │   │   ├── kubedns-amd64.tar
    │   │   │   │   ├── kube-dnsmasq-amd64.tar
    │   │   │   │   ├── kube-proxy.tar
    │   │   │   │   ├── kubernetes-dashboard.tar
    │   │   │   │   ├── kube-scheduler.tar
    │   │   │   │   └── pause-amd64.tar
    │   │   │   ├── kubectl
    │   │   │   ├── kubelet
    │   │   │   └── lib
    │   │   │       ├── pip-8.1.2.tar.gz
    │   │   │       └── setuptools-28.6.0.tar.gz
    │   │   ├── handlers
    │   │   │   └── main.yml
    │   │   ├── meta
    │   │   │   └── main.yml
    │   │   ├── README.md
    │   │   ├── tasks
    │   │   │   ├── centos_install.yml
    │   │   │   ├── main.yml
    │   │   │   └── ubuntu_install.yml
    │   │   ├── templates
    │   │   │   └── master
    │   │   │       ├── addons
    │   │   │       │   ├── calico
    │   │   │       │   │   ├── calico-tls.yaml.j2
    │   │   │       │   │   └── calico.yaml.j2
    │   │   │       │   ├── dashboard
    │   │   │       │   │   ├── authentication
    │   │   │       │   │   │   ├── dashboard-controller.yaml.j2
    │   │   │       │   │   │   └── dashboard-service.yaml.j2
    │   │   │       │   │   ├── dashboard-controller.yaml.j2
    │   │   │       │   │   └── dashboard-service.yaml.j2
    │   │   │       │   └── dns
    │   │   │       │       └── 1.4.1
    │   │   │       │           ├── authentication
    │   │   │       │           │   ├── skydns-rc.yaml.j2
    │   │   │       │           │   └── skydns-svc.yaml.j2
    │   │   │       │           ├── skydns-rc.yaml.j2
    │   │   │       │           └── skydns-svc.yaml.j2
    │   │   │       ├── config
    │   │   │       │   ├── authentication
    │   │   │       │   │   └── kubelet.conf.j2
    │   │   │       │   └── kubelet.conf.j2
    │   │   │       ├── manifests
    │   │   │       │   ├── authentication
    │   │   │       │   │   ├── etcd.json.j2
    │   │   │       │   │   ├── flannel.yaml.j2
    │   │   │       │   │   ├── kube-apiserver.json.j2
    │   │   │       │   │   ├── kube-controller-manager.json.j2
    │   │   │       │   │   ├── kube-proxy.json.j2
    │   │   │       │   │   └── kube-scheduler.json.j2
    │   │   │       │   ├── etcd.json.j2
    │   │   │       │   ├── flannel.yaml.j2
    │   │   │       │   ├── kube-apiserver.json.j2
    │   │   │       │   ├── kube-controller-manager.json.j2
    │   │   │       │   ├── kube-proxy.json.j2
    │   │   │       │   └── kube-scheduler.json.j2
    │   │   │       └── supervisor
    │   │   │           └── kubelet.conf.j2
    │   │   ├── tests
    │   │   │   ├── inventory
    │   │   │   └── test.yml
    │   │   └── vars
    │   │       └── main.yml
    │   └── swqk8snode
    │       ├── defaults
    │       │   └── main.yml
    │       ├── files
    │       │   ├── cni
    │       │   │   └── bin
    │       │   ├── images
    │       │   │   ├── flannel.tar
    │       │   │   ├── kube-proxy.tar
    │       │   │   └── pause-amd64.tar
    │       │   ├── kubelet
    │       │   └── lib
    │       │       ├── pip-8.1.2.tar.gz
    │       │       └── setuptools-28.6.0.tar.gz
    │       ├── handlers
    │       │   └── main.yml
    │       ├── meta
    │       │   └── main.yml
    │       ├── README.md
    │       ├── tasks
    │       │   ├── centos_install.yml
    │       │   ├── main.yml
    │       │   └── ubuntu_install.yml
    │       ├── templates
    │       │   └── node
    │       │       ├── config
    │       │       │   ├── authentication
    │       │       │   │   └── kubelet.conf.j2
    │       │       │   └── kubelet.conf.j2
    │       │       ├── manifests
    │       │       │   ├── authentication
    │       │       │   │   ├── flannel.yaml.j2
    │       │       │   │   └── kube-proxy.json.j2
    │       │       │   ├── flannel.yaml.j2
    │       │       │   └── kube-proxy.json.j2
    │       │       └── supervisor
    │       │           └── kubelet.conf.j2
    │       ├── tests
    │       │   ├── inventory
    │       │   └── test.yml
    │       └── vars
    │           └── main.yml
    └── setup.yml

Requirements
------------
+   需要把二进制的kubelet和kubctl copy到role file下
+   需要把相关容器save 到 role file image下或者注释load直接修改var进行pull （https://hub.docker.com/r/swqmaven）
+   Ansible1.4 版本+

Role Variables
--------------
      pod_infra_container_image_name: gcr.io/google_containers/pause-amd64:3.0
      etcd_image_name: gcr.io/google_containers/etcd-amd64:2.2.5
      kube_apiserver_image_name: gcr.io/google_containers/kube-apiserver-amd64:v1.4.1
      kube_controller_manager_image_name: gcr.io/google_containers/kube-controller-manager-amd64:v1.4.1
      kube_scheduler_image_name: gcr.io/google_containers/kube-scheduler-amd64:v1.4.1
      kube_proxy_image_name: gcr.io/google_containers/kube-proxy-amd64:v1.4.1
      flannel_image_name: quay.io/coreos/flannel:v0.6.2-amd64
      kubedens_image_name: gcr.io/google_containers/kubedns-amd64:1.8
      dnsmasq_image_name: gcr.io/google_containers/kube-dnsmasq-amd64:1.4
      exechealthz_image_name: gcr.io/google_containers/exechealthz-amd64:1.2
      kubernetes_dashboard_image_name: gcr.io/google_containers/kubernetes-dashboard-amd64:v1.4.0

      cluster_name: kubernetes      
      etcd_servers: http://127.0.0.1:2379
      kubernetes_master_url: http://127.0.0.1:8080
      kubernetes_master_url_tls: https://127.0.0.1:443
      kubernetes_master_ip: 127.0.0.1:8080
      service_cluster_ip_range: 100.64.0.0/12
      flannel_network_config: "{ \"Network\": \"172.24.0.0/13\", \"Backend\": { \"Type\": \"vxlan\", \"VNI\": 1 } }"
      flannel_etcd_prefix: /coreos.com/network
      kubernetes_ca_crt_CN: swqmaven.com
      kubernetes_cluster_default_token: 1MVytSxl5f2X05fjnZGmvFiXkixYQUgq
      kubernetes_cluster_default_user: swqmaven
      kubernetes_cluster_default_uid: 000c2968d923
      kubernetes_cluster_default_group: system:kubelet-bootstrap
      cluster_dns: 100.64.0.10
      cluster_domain: cluster.local

      

Examples
--------


Dependencies
------------

None

License
-------

BSD

Author Information
------------------

swqmaven
 

