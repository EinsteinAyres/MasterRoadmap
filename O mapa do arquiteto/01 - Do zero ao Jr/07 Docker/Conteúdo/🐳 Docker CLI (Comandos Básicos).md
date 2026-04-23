  
## 📌 Conceito  
Docker CLI é a interface de linha de comando para gerenciar containers, imagens e recursos.  
  
---  
  
## ⚙️ Comandos principais  
  
### 🔹 Imagens  
docker pull nginx  
docker images  
docker rmi <id>  
  
### 🔹 Containers  
docker run -d -p 8080:80 nginx  
docker ps  
docker stop <id>  
docker rm <id>  
  
### 🔹 Execução  
docker exec -it <container> bash  
  
---  
  
## 🔥 Flags importantes  
- -d → background  
- -p → porta  
- -it → modo interativo  
- --name → nome do container  
  
---  
  
## ⚠️ Boas práticas  
- Sempre nomear containers  
- Evitar rodar como root  
- Limpar containers antigos  
  
---  
  
## 🧠 Flashcards  
  
O que faz docker run?::Cria e executa um container  
  
Diferença entre docker stop e docker rm?::Stop para, rm remove  
  
Para que serve docker exec?::Executar comando dentro do container  
  
O que faz -p 8080:80?::Mapeia porta local para container  
  
---  
  
## 🔗 Conexões  
- [[🧾 Dockerfile]]  
- [[⚙️ Docker Compose]]