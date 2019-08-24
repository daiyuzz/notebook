修改 /etc/nginx/sites-available文件，新建一个配置文件，代码如下
server{
        listen 80;
        server_name 127.0.0.1;
        location /api{

                include uwsgi_params;
                #proxy_pass http://127.0.0.1:5000;
                #uwsgi_pass 127.0.0.1:8899;
                uwsgi_pass unix:/home/daiyu/Documents/flask实战/iSurvey/backend/mysite.sock;

        }

        location /{
                root /home/daiyu/Documents/flask实战/iSurvey/frontend;
                index index.html index.htm;
        }

}
导入uwsgi文件，使用uwsgi文件进行连接；
location部分为静态连接

和  /etc/nginx/sites-enabled文件（软连接，使用ln -s 被连接文件目录 目标目录）
创建软连接后会生成一个和sites-avaliabled中一样的配置文件（软连接）
用 sudo nginx -s reload来重新加载配置文件
