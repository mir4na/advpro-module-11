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

## Reflection on Rolling Update & Kubernetes Manifest File
----
1. What is the difference between Rolling Update and Recreate deployment strategy?

    Rolling Update dan Recreate adalah dua strategi deployment yang berbeda dalam Kubernetes. Rolling Update melakukan pembaruan aplikasi secara bertahap dengan mengganti pod lama dengan pod baru satu per satu, sehingga aplikasi tetap tersedia selama proses update dan tidak mengalami downtime. Sebaliknya, Recreate strategy akan menghentikan semua pod yang sedang berjalan terlebih dahulu, kemudian membuat pod baru dengan versi yang diperbarui, yang mengakibatkan aplikasi mengalami downtime selama proses deployment berlangsung.

2. Try deploying the Spring Petclinic REST using Recreate deployment strategy and document your attempt.

    Untuk menerapkan Recreate deployment strategy, saya perlu memodifikasi file deployment.yaml dengan menambahkan spesifikasi strategy: type: Recreate di bagian spec. Ketika strategy ini diterapkan, semua pod dengan versi lama akan dihentikan secara bersamaan sebelum pod baru dibuat, yang menyebabkan aplikasi tidak dapat diakses untuk sementara waktu. Proses ini lebih cepat dalam hal pergantian versi tetapi mengorbankan availability aplikasi, berbeda dengan rolling update yang menjaga aplikasi tetap berjalan selama proses update.

3. Prepare different manifest files for executing Recreate deployment strategy.

    Untuk mengimplementasikan Recreate strategy, file deployment.yaml perlu dimodifikasi dengan menambahkan bagian strategy di bawah spec, yaitu strategy: type: Recreate. Berbeda dengan Rolling Update yang memiliki konfigurasi maxSurge dan maxUnavailable, Recreate strategy tidak memerlukan parameter tambahan karena sifatnya yang menghentikan semua pod lama sebelum membuat yang baru. File service.yaml tidak perlu diubah karena konfigurasi service tidak bergantung pada deployment strategy yang digunakan.

4. What do you think are the benefits of using Kubernetes manifest files? Recall your experience in deploying the app manually and compare it to your experience when deploying the same app by applying the manifest files (i.e., invoking `kubectl apply -f` command) to the cluster.

    Kubernetes manifest files memberikan banyak keuntungan dibandingkan dengan deployment manual menggunakan perintah kubectl secara langsung. Dengan manifest files, konfigurasi deployment menjadi lebih konsisten, dapat diversion control, mudah di-reproduce di environment yang berbeda, dan memungkinkan automation dalam CI/CD pipeline. Selain itu, manifest files juga memudahkan dalam melakukan rollback, sharing konfigurasi antar tim, dan dokumentasi infrastruktur sebagai kode (Infrastructure as Code), sedangkan deployment manual rentan terhadap human error dan sulit untuk diduplikasi dengan konsisten.