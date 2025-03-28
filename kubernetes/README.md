# Kubernetes (Giriş)

Bu bölüm, konteynerleştirilmiş uygulamaların büyük ölçekte dağıtımı, yönetimi ve ölçeklendirilmesi için kullanılan açık kaynaklı bir orkestrasyon platformu olan Kubernetes'e (genellikle K8s olarak kısaltılır) bir giriş yapmaktadır.

**Not:** Kubernetes oldukça kapsamlı ve karmaşık bir sistemdir. Bu bölüm sadece temel kavramları ve mimariyi tanıtmayı amaçlamaktadır, derinlemesine bir rehber değildir.

**İncelenecek Konular:**

*   Konteyner Orkestrasyonu Nedir? Neden Kubernetes?
*   Kubernetes Mimarisi (Control Plane: API Server, etcd, Scheduler, Controller Manager; Node Components: Kubelet, Kube-proxy, Container Runtime)
*   Temel Kubernetes Nesneleri (Objects):
    *   Pods: Konteynerlerin çalıştığı temel birim.
    *   Services: Pod'lara ağ erişimi sağlayan soyutlama katmanı (ClusterIP, NodePort, LoadBalancer).
    *   Deployments: Pod'ların ve ReplicaSet'lerin bildirimsel (declarative) yönetimini sağlar (ölçeklendirme, güncellemeler).
    *   ReplicaSets: Belirli sayıda Pod replikasının her zaman çalışır durumda olmasını sağlar.
    *   Namespaces: Küme içindeki kaynakları mantıksal olarak ayırmak için kullanılır.
    *   ConfigMaps & Secrets: Yapılandırma verilerini ve hassas bilgileri yönetmek için.
    *   Volumes: Konteynerler için kalıcı depolama sağlamak için.
*   `kubectl` Komut Satırı Aracı: Kubernetes kümesiyle etkileşim kurmak için kullanılan ana araç.
*   Temel `kubectl` Komutları (`get`, `describe`, `create`, `apply`, `delete`, `logs`, `exec`).
*   YAML Manifest Dosyaları: Kubernetes nesnelerini bildirimsel olarak tanımlamak için kullanılır.
*   Yerel Kubernetes Kurulumları (Minikube, kind, K3s)
*   Bulut Sağlayıcıların Yönetilen Kubernetes Servisleri (EKS, AKS, GKE)
