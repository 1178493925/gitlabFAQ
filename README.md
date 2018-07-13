# gitlab��װ������

## ��װgitlab ����centos 6 Ϊ����

ֻ��Ҫ�������ٷ�˵���ĵ�һ��ʵ���Ͽ���ʡ�ԣ�������淢�������⣬��yum��������������ʵ������������װ�ˡ�

### ���gitlab yumԴ

    curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.rpm.sh | sudo bash

### yum��װ

sudo EXTERNAL_URL="http://gitlab.xxoo" yum -y install gitlab-ce

### �״η��� �����ͨ��

��������������� gitlab.xxoo��������ʻ���hosts����һ��

GitlabIP gitlab.xxoo

Ȼ���������gitlab.xxoo���ʣ��û���root����������8���ַ���½��Ȼ���޸����롣

## Դ�밲װ (һ��û��Ҫ)
���ٷ��ĵ���[https://docs.gitlab.com/ce/install/installation.html]


## docker��װ

���ٷ��ĵ���[https://docs.gitlab.com/ce/install/docker.html]

## ���͵�gitlab����

```
external_url 'http://git.YOUdomain'
gitlab_rails['gitlab_shell_ssh_port'] = 8888
nginx['listen_port'] = 5580

gitlab_rails['smtp_enable'] = true
gitlab_rails['smtp_address'] = "smtp.qq.com"
gitlab_rails['smtp_port'] = 465
gitlab_rails['smtp_user_name'] = "git@YOUdomian"
gitlab_rails['smtp_password'] = "YOUpasswd"
gitlab_rails['smtp_authentication'] = "login"
gitlab_rails['smtp_enable_starttls_auto'] = true
gitlab_rails['smtp_tls'] = true
gitlab_rails['gitlab_email_from'] = 'gitYOUdomian'
```
˵����

����Ǽ�����Ҫ�Լ����õĲ���

��һ����url��������ʾ����gitlabҳ�������ǵ�url

�ڶ������ֱ�����Ҫ�Զ����ssh��web�˿�

## ������Ч����������

    gitlab-ctl reconfig
    gitlab-ctl restart

ע��gitlab�κ����ö���������`/etc/gitlab/gitlab.rb`�����޸�

�޸ĺ�gitlab-ctl reconfig ���Զ����ɸ������ֵ����ã���Ҫ����ȥ���������ֵ����ã�
����Ϊ���޸�web�˿�ȥ�޸�nginx���ã��Ƕ��Ǵ���ķ������ܶ�blog����������ģ�
������ȫ����ķ�����reconfig�ͻᶪ����
`
# 1.gitlab ʹ��API���������������Ŀ

##  ��������

����һ���ļ�������������д�룬һ��һ����

    vim group

ʹ��gitlab��API��������Ⱥ�飺

```

for i in `cat group`; do 

echo $i": ";
curl --request POST --header "PRIVATE-TOKEN: 9koXXXXOOOOOs5t"  --data "name=${i}&path=${i}" http://IPofgitlab/api/v4/groups;

done

```

## ������Ŀ


ʹ��gitlab��API����������Ŀ�ֿ�:

д������Ⱥ��XXX��������Ŀ���ļ�repo�У�

vi repo


��÷���������ռ�id������Ϊ9��ʹ��gitlab��API�����������������ռ�ipΪ9�Ĳֿ⣺


```
for i in `cat repo`; do
curl --request POST --header "PRIVATE-TOKEN: 9koXXXXOOOOOs5t"  --data "name=${i}&namespace_id=9" http://IPofgitlab/api/v4/projects;
done

```

# 2.repo��Ǩ�Ƽ���������gitlab��


## ����

��������������ⲿgit��������ʹ��repo���д�����£����ڴ������󡢿�ࡢ��˾���绷����Ӱ�죬�ӷ�������ȡ��������ٶȻ�����������jenkins��gitlab���ڱ���linux����������repo�汾�⣬���ⲿ�������������屣�浽���ؾ����������������뿪��ʱ�ӱ��ط�����ȡ�ô���ʹ�á�

## ʵʩ


### ȡ���ⲿ�������ľ���� ��your_url Ϊ�ⲿ���ַ��


��Ϊ���������200�����������,ֱ��ʹ��repo��ȡ�����,׷��--mirror�������������������滻Ϊ���repo·��

```
mkdir repo_mirror

cd repo_mirror

```

### ��ʼ���汾�⣺

repo init --mirror -u IPofgitlab/manifests.git--repo-IPofgitlab/repo.git -mmanifest.xml


### ͬ����

    repo sync

ͬ����ɺ󣬽������潫����repo�����ľ������õ�repo_mirrorĿ¼�¡�


### ȡ�����д�����ļ���,������������project


repo_mirrorĿ¼ִ�У�ls >../projects_list.txt

��projects_list.txt��projects_list.txt ������.git �滻Ϊ \ 

    perl  -i -lpe '#\.git# \#'  projects_list.txt

````
�滻ǰ��

pro1.git
pro2.git
pro3.git

�滻��

pro1 \
pro2 \
pro3 \
````


### ʹ��gitlab api������ָ��groups�´���project��



���������http://IPofgitlab/api/v4/projects ������Ҫgroups��ID ����ӦΪnamespace��id��ȡ�ñ��δ�����Ҫ����idֵΪ9��

����ͨ��namespace�ӿڻ�ȡ��list

    http://IPofgitlab/api/v4/namespaces



### ����gitlab��projects

````
    projects="pro1 \
    pro2 \
    pro3 "
     
    for project in $projects
    do
        info="name=$project&path=$project&wiki_enabled=no&public_jobs=true&public=true&namespace_id=2&default_branch=master&private_token=your_private_token"
        curl -d $info "http://your_ip/api/v3/projects"
    done

````

ִ�н����󵽵�¼��gitlab��ȷ���ⴴ����ɡ�


###  ��������ϴ�������gitlab��

```

    dir_name="$PWD"

    all=`ls ${dir_name}`
    for i in $all
    do
    if [ -d $i ]
    then
    echo "[$i]"
    cd $i
    git push --mirror git@your_ip:git/$i
    cd ..
    fi
    done 

```

ִ�нű���ȷ���汾��push����


### ����ʹ�ñ��ؿ����repo����ȡ�� ��your_local_urlΪ����gitlab���ַ��


    repo init -u your_local_url/manifests.git--repo-urlyour_local_url/repo.git -mmanifest.xml

ͬ����

   repo sync

# 3.gitlab����Ŀ¼Ǩ��


gitlab��������Ĭ��Ŀ¼��/var/opt/gitlab/git-data/repositories

����Ŀ¼����һ������̵�Ŀ¼(Ŀ¼�������ϲ������)

````
makedir  -p /data/gitlab-data
mount sdb  data/gitlab-data
````

## �������裺

### ֹͣ����������ӷ���

    gitlab-ctl stop unicorn   
    gitlab-ctl stop sidekiq

## ����Ǩ��

    cp -r -p /var/opt/gitlab/git-data/repositories/ /home/gitlab-data/


����CPһ��Ҫ����-p��������Ȼ�ᵼ��Ȩ������


### �������� /etc/gitlab/gitlab.rb

vim /etc/gitlab/gitlab.rb

#ָ������Ŀ¼

git_data_dir "/home/gitlab-data"


### ʹ������Ч

     gitlab-ctl reconfigure


### ����gitlab

     gitlab-ctl restart



##  ����502����취


### 8080 �˿ڳ�ͻ


ԭ������unicornĬ��ʹ�õ��� 8080 �˿ڡ�

����취���� /etc/gitlab/gitlab.rb ,
ȥ�� # unicorn['port'] = 8080 ��ע�ͣ��� 8080 �޸�Ϊ 9090 ����������� sudo gitlab-ctl reconfigure ���ɡ�

### ����ڴ��С��������Ӳ����Դ̫С

������������̽������ڴ��ڼ�������ᵼ��502����һ��ʱ�䣬�����������ڷ��ʾ�ok��


# ������Ϣ & Url����


### gitlab-runner�ĵ�

https://gitlab.com/gitlab-org/gitlab-runner/tree/master/docs

### Security��ȫ�ĵ�

https://gitlab.com/help/security/README.md

### gitlab ����

www.gitlab.com

### gitlab QQ����Ⱥ

208598995



