## Reflection on Hello Minikube
----
1. Compare the application logs before and after you exposed it as a Service. Try to open the app several times while the proxy into the Service is running. What do you see in the logs? Does the number of logs increase each time you open the app?

    Sebelum aplikasi di-expose sebagai Service, log aplikasi hanya menunjukkan bahwa HTTP server telah dimulai pada port 8080 dan UDP server pada port 8081, tanpa ada aktivitas request yang masuk. Setelah aplikasi di-expose sebagai Service dan dapat diakses melalui browser via proxy tunnel yang dibuat oleh minikube, setiap kali aplikasi dibuka di browser akan menghasilkan log tambahan yang menunjukkan adanya HTTP request yang masuk ke server. Ya, jumlah log akan bertambah setiap kali aplikasi dibuka karena setiap akses melalui browser akan menciptakan HTTP request baru yang tercatat dalam log aplikasi.

2. Notice that there are two versions of `kubectl get` invocation during this tutorial section.
   - The first does not have any option, while the latter has `-n` option with value set to
`kube-system`.
   - What is the purpose of the `-n` option and why did the output not list the pods/services that you
explicitly created?

    Opsi -n pada kubectl digunakan untuk menentukan namespace tertentu yang ingin ditampilkan, dalam hal ini kube-system adalah namespace sistem Kubernetes yang berisi komponen-komponen inti seperti DNS, metrics server, dan komponen control plane lainnya. Ketika menggunakan kubectl get pods -n kube-system, output tidak menampilkan pods/services yang dibuat secara eksplisit (seperti hellonode) karena pods tersebut dibuat di namespace default, sedangkan perintah tersebut hanya menampilkan resource yang berada di namespace kube-system. Namespace dalam Kubernetes berfungsi sebagai cara untuk memisahkan dan mengorganisir resource dalam cluster yang sama, sehingga resource di namespace berbeda tidak akan saling terlihat kecuali secara eksplisit ditentukan.
