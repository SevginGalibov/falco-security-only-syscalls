# 🎯 Kubernetes Falco Security - Gürültüsüz, Temiz Güvenlik İzleme

Bu repo; **Kubernetes ortamında Falco Security ile minimal, odaklanmış ve görsel destekli güvenlik izleme** kurulumunu dokümante eder. Amaç: sadece **kritik güvenlik olaylarını anlamlı şekilde izlemek**, **gürültüsüz çalışmak** ve **Teams ile anlık bildirim almak**.

![Falco Architecture](https://falco.org/assets/img/falco-architecture.png)

---

## 🚀 Proje Amacı

✅ **Sadece önemli güvenlik olaylarını izlemek.**

✅ **Varsayılan kurallar yerine kendi kritik odaklı custom kurallar ile izlemek.**

✅ **Microsoft Teams entegrasyonu ile anlık alarm almak.**

✅ **Falcosidekick WebUI + Redis ile görsel alarm takibi.**

✅ **Gürültüsüz, sade, operasyonel anlamlı loglama.**

---

## 📦 Kullanılan Yapılandırmalar

### 📌 **Custom Rules (`custom-rules.yaml`)**

* **Pod içerisinde shell/exec açma algılama**
* **Pod içerisinde netcat / nmap gibi tarama araçlarının kullanımı**
* **Pod içerisinde touch, mkdir gibi dosya oluşturma denemeleri**

### 📌 **Falco Helm Values (`falco-values.yaml`)**

* Syscall izleme aktif ✅
* Gürültü önlemek için sadece kendi kurallarım aktif ✅
* Rate limit ile aşırı log baskısı önlendi ✅
* K8S metadata enrich aktif ✅
* TTY attach algılama aktif ✅
* Falcosidekick WebUI ✅ ve Redis backend ✅
* Microsoft Teams entegrasyonu ✅ sadece Critical seviyesinde

---

## ⚙️ Helm Kurulum Komutu

```bash
helm upgrade --install falco falcosecurity/falco \
--namespace falco --create-namespace \
-f falco-values.yaml \
-f custom-rules.yaml \
--set falcosidekick.config.teams.webhookurl="https://blabla.webhook.office.com/..." \
--set falcosidekick.config.teams.minimumpriority=Critical \
--set falcosidekick.config.teams.outputformat=all
```

---

## 📌 **Redis Ne İşe Yarar?**

WebUI tarafında Redis, geçmiş logların saklanmasını sağlar. Pod restart durumunda UI hafızalı kalır.

## 📌 **Teams Entegrasyonu Neden?**

Anlık aksiyon alınabilecek kritik güvenlik uyarılarını operasyon ekipleriyle buluşturur. Sadece **critical** öncelikte, anlamlı log gider.

## 📌 **Rate Limit Neden?**

Log fırtınasına karşı koruma sağlar, hem WebUI hem Teams tarafında maksimum 1 event/saniye gönderir.

## 📌 **TTY Neden Aktif?**

Pod içerisine `kubectl exec -it pod bash` gibi shell açılmaları izlemek için.

## 📌 **Collectors Kubernetes Neden Aktif?**

`k8smeta` plugin ile zengin metadata bilgisi loglara eklenir, sadece syscall için enrich yapılır.

---

## ✅ Özet

Bu kurulum ile:

* 🔥 **Container içerisinde kritik güvenlik aktiviteleri izlenir.**
* 🔥 **Microsoft Teams’e anlamlı alarm gider.**
* 🔥 **WebUI ile görsel takip yapılır.**
* 🔥 **Gürültüsüz, verimli, temiz güvenlik görünürlüğü sağlanır.**

---
## 🌐 Local UI Erişimi (Port Forwarding)

Uygulamayı local erişime açmak için terminalden aşağıdaki komutu kullanabilirsiniz yada ingress oluşturarak hostname üzerinden erişebilirsiniz.:

```bash
kubectl port-forward svc/falcosidekick-ui 2801:2801 -n falco
![Dashboard Görünümü](./images/dashboard-example.png)
```
## 📊 Uygulama Arayüzü ve alarm görselleri

![UI Dashboard Görünümü](https://imgur.com/sZqczOJ)

![UI Json Görünümü](https://imgur.com/ALQgRLu)

![Teams Alarm Görünümü]( (https://imgur.com/biMMUgN)


## 📚 Ek Kaynaklar

* [Falco Official Documentation](https://falco.org/docs/)
* [Falcosidekick GitHub](https://github.com/falcosecurity/falcosidekick)
* [Falcosidekick UI](https://github.com/falcosecurity/falcosidekick-ui)
