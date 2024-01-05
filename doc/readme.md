# exporter palybooks 使用

1 roles目录下为各exporter playbook，后续如果有新的exporter也放入该目录

2 deploy目录下yaml为各个exporter的playbook入口文件，文件中的roles参数对应roles下目录

注：在创建task时指定该yaml文件，但是在测试时把这些文件放到了项目根目录下，这样只需要指定文件名如node_exporter.yaml，未测试使用相对路径如deploy/node_exporter.yaml是否能正确找到roles

3 下载好各exporter的二进制文件，放到指定目录下

## 部署时配置的变量如下:

注：在创建task时勾选“允许任务中自定义CLI参数”，参数为数组形式如["-e", "var1=value1", "-e", “var2=value2”]

### 1 node_exporter

defaults 参数参见roles/node_exporter/defaults/main.yml

必须指定参数：  
node_exporter_binary_local_dir  #源二进制文件所在目录

可选参数：  
node_exporter_web_listen_address #服务监听IP及端口  

### 2 mysql_exporter
defaults 参数参见roles/mysqld_exporter/defaults/main.yml

必须指定参数：  
mysqld_exporter_binary_local_dir #源二进制文件所在目录  
mysqld_exporter_username # mysql用户名  
mysqld_exporter_password # mysql密码  
注：该用户至少需要有一下权限  GRANT PROCESS, REPLICATION CLIENT, SELECT ON *.* TO 'exporter'@'localhost';

可选参数：  
mysqld_exporter_web_listen_address #服务监听IP及端口  

### 3 nginx_exporter
defaults参数参见roles/ansible-role-nginx_exporter/defaults/main.yml

nginx  需要配置  
location = /nginx_stats {  
stub_status;  
}

必须指定参数：  
nginx_exporter_binary_local_dir #源二进制文件所在目录  
nginx_exporter_status_endpoint  # nginx 状态url,如http://localhost/nginx_stats 

### 4 process_exporter
defaults参数参见roles/ansible-process_exporter/defaults/main.yml  

必须指定参数：  
exporter_binary_local_dir #源二进制文件所在目录 

## Prometheus 配置

指定时间间隔，动态更新

主配置文件：  
- job_name: 'exporter'
  file_sd_configs:
    - files: ['/path/to/*.yaml']
      refresh_interval: 5s

动态加载的文件  

- targets: ["ip:port"]
  labels:
     exporter: xxx-exporter

