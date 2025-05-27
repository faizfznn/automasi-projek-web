pipeline {
    agent any // Menjalankan di agent Jenkins manapun yang tersedia

    environment {
        // Direktori target di web server (Nginx)
        DEPLOY_PATH = '/var/www/html/automasi-projek-web'
    }

    stages {
        stage('1. Checkout Code') {
            steps {
                echo 'Mencoba mengambil kode dari GitHub...'
                checkout scm
                echo 'Kode berhasil diambil.'
            }
        }

        stage('2. Clean Old Deployment (Opsional)') {
            steps {
                echo "Membersihkan direktori deployment lama di ${env.DEPLOY_PATH} jika ada..."
                // Hati-hati dengan rm -rf! Pastikan path benar.
                // Jenkins user mungkin butuh sudo. Ini akan kita atasi nanti.
                // Untuk sekarang, kita akan deploy ke subfolder di /var/www/html
                // atau kita bisa atur permission /var/www/html agar jenkins bisa menulis.
                // Alternatif: copy dengan rsync dan opsi --delete
                sh "sudo rm -rf ${env.DEPLOY_PATH}/*" // Perlu konfigurasi sudo untuk jenkins
            }
        }

        stage('3. Deploy to Web Server (Nginx)') {
            steps {
                echo "Menyalin file aplikasi ke ${env.DEPLOY_PATH}..."
                // Pastikan direktori target ada dan Jenkins punya izin tulis
                // Kita buat direktori jika belum ada
                sh "sudo mkdir -p ${env.DEPLOY_PATH}"
                sh "sudo cp -R * ${env.DEPLOY_PATH}/" // Menyalin semua dari workspace ke target
                echo "Aplikasi berhasil di-deploy ke Nginx."
            }
        }

        stage('4. Test Ketersediaan Aplikasi') {
            steps {
                echo "Menguji apakah aplikasi bisa diakses..."
                // Ini tes yang sangat sederhana, hanya cek apakah URL mengembalikan status 200 OK
                // Kita asumsikan aplikasi akan diakses via <IP_VM>/automasi-projek-web/
                // Jika kamu deploy langsung ke root /var/www/html, sesuaikan URL
                sh "curl -sSf http://localhost/automasi-projek-web/index.html || exit 1"
                echo "Tes ketersediaan aplikasi berhasil."
            }
        }
    }

    post {
        always {
            echo 'Pipeline selesai.'
            // Clean up workspace Jenkins
            cleanWs()
        }
        success {
            echo 'Pipeline berhasil!'
        }
        failure {
            echo 'Pipeline gagal!'
            // Di sini kamu bisa menambahkan notifikasi (misal: email)
        }
    }
}