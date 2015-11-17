# Repo Ayarları

yum ile sistemimize kurduğumuz RPM paketlerini indirdiğimiz kaynağa Linux sistemler üzerinde *repository* ismi verilmektedir. Repositoryler dağıtımların resmi kaynakları olabileceği gibi, farklı firma veya şahısların geliştirdiği yazılımları dağıtmak için çeşitlendirilebilir de.

Repository (kısaca *repo*) ayarları Red Hat tabanlı sistemlerde ```/etc/yum.repos.d``` dizini altındaki dosyalarda saklanır.

Örneğin EPEL (Extra Packages for Enterprice Linux) repoları, resmen Redhat/CentOS parçası olmamakla birlikte, Linux kullanıcıları topluluğunun sağladığı pek çok pakedi barındırır.

EPEL reposunu sistemimize eklediğimizde, daha önce erişemediğimiz pek çok pakete (örneğin phpmyadmin) erişebiliriz.

EPEL reposunu sistemimize eklemek için, *wget* ile rpm dosyasını edinmek, arından bu rpm dosyasını sistemimize *yüklemek* gerekir.

```bash
## RHEL/CentOS 6 64-Bit ##
[root@emre ~]# wget http://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
[root@emre ~]# rpm -ivh epel-release-6-8.noarch.rpm
```

Yukarıdaki komutun ardından sistemimizde EPEL reposu aktif olacaktır. Örneğimiz için kullandığımız rpm dosyasının RedHat/CentOS 6 için ve 64bit mimariler için olduğunu unutmayın. Kendi sisteminiz farklılık gösteriyorsa, farklı dosya indirmeniz gerekecektir.