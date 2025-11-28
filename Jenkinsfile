pipeline {
    agent any
    
    stages {
        stage('Checkout Git') {
            steps {
                echo '🎯 Récupération du code depuis GitHub...'
                git branch: 'main', 
                url: 'https://github.com/nesline1chiemgne/mon-site-web'
            }
        }
        
        stage('Validation Fichiers') {
            steps {
                echo '🔍 Validation de la structure des fichiers...'
                sh '''
                    echo "=== Fichiers présents ==="
                    ls -la
                    echo "=== Fichiers HTML ==="
                    find . -name "*.html"
                    echo "=== Contenu dossier CSS ==="
                    ls -la css/
                '''
            }
        }
        
        stage('Tests') {
            steps {
                echo '🧪 Exécution des tests...'
                sh '''
                    echo "Vérification des fichiers HTML..."
                    for file in *.html; do
                        if [ -f "$file" ]; then
                            echo "✅ $file - Fichier présent"
                        else
                            echo "❌ $file - Fichier manquant"
                            exit 1
                        fi
                    done
                '''
            }
        }
        
        stage('Déploiement') {
            steps {
                echo '🚀 Déploiement sur Apache...'
                sh '''
                    # Nettoyer le répertoire web
                    sudo rm -rf /var/www/html/*
                    
                    # Copier les nouveaux fichiers
                    sudo cp -r *.html css/ /var/www/html/
                    
                    # Configurer les permissions
                    sudo chown -R www-data:www-data /var/www/html/
                    sudo chmod -R 755 /var/www/html/
                
                    # Redémarrer Apache
                    sudo systemctl restart apache2
                    
                    echo "✅ Déploiement terminé avec succès!"
                '''
            }
        }
        
        stage('Vérification') {
            steps {
                echo '🔎 Vérification du déploiement...'
                sh '''
                    echo "=== Fichiers déployés ==="
                    sudo ls -la /var/www/html/
                    echo "=== Accès au site ==="
                    curl -I http://localhost || echo "Test d'accès au site"
                '''
            }
        }
    }
    
    post {
        always {
            echo '📊 Nettoyage et rapports...'
            publishHTML([
                allowMissing: false,
                alwaysLinkToLastBuild: true,
                keepAll: true,
                reportDir: '.',
                reportFiles: 'index.html',
                reportName: 'Rapport du Site Web'
            ])
        }
        success {
            echo '🎉 Pipeline réussi! Site web déployé.'
            echo '🌐 Accédez au site: http://192.168.56.10'
        }
        failure {
            echo '❌ Pipeline échoué! Vérifiez les logs.'
        }
    }
}