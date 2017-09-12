---
title: CI模型-视图-控制器 命名规范
date: 2015-08-13 19:47
tags: CI
categories: PHP编程
---

### controller：
    控制器类名必须以大写字母开头
             class  User  extends  CI_Controller{}          //建议写法
             class  BlogUser  extends  CI_Controller{}  //建议写法  
     以下这样写是无效的
             class  blog  extends  CI_Controller{}
             class  blogUser  extends  CI_Controller{}

     控制器类的方法没有要求

     控制器文件名也没有要求，但是建议全部小写，且注意文件名和类名同一个单词
     controller/blog.php 、controller/bloguser.php 

### model
    模型类名首字母也必须大写，其他小写
    例如：
    class  Blog_model  extends  CI_Model {    //class BlogModel extends  CI_Model也可
        function __construct(){
            parent::__construct();
        }
    }

    载入model以及使用model
    $this->load->model('Blog_model');    //model类名  $this->load->model('BlogModel'); 
    $this->Blog_model->function();         //载入之后可直接通过类名访问其方法$this->BlogModel->function();

    模型的文件名没有要求，但是建议全部小写,且注意文件名和类名同一个单词 
    例如：model/blog_model.php   //       model/blogmodel.php
    
### view
    文件名是 views/viewname.php
    载入视图 $this->load->view("viewname");
    总之：文件名和载入一致即可，大小写没有特别要求
    例如：文件名为views/viewName.php  载入时$this->load->view("viewName");

    用子文件夹存储视图
    views/user/bloguser.php   载入时$this->load->view("user/bloguser");



    总结，对于模型和控制器，类名的首字母必须大写，其他字母无要求，可根据个人习惯
              文件名没有特别要求，建议首字母小写或全小写，名字与类名一致（除大小写可不一致）载入的模型和模型类名一致

              对于视图，文件名和载入时一致即可，载入时不带后缀 

      因为model和controller如果很多，载入model时不可能一个一个打开查看其类名，但是可以快速根据文件名确定其类名

      那就要求对文件的命名和类的命名要有一个一致的规范

    我的习惯（保持）
      【1】controller,类名User或UserBlog，文件名user.php或userblog.php
      【2】model，类名UserModel或UserBlogModel ，文件名usermodel.php或userblogmodel.php
       载入方法$this->load->model("UserModel");
       用在构造方法里如下：
       class BlogModel  extends  CI_Controller{
           function __construct(){
                parent::__construct();
                $this->load->model('UserModel'); 
            }
        }
      【3】view，文件名userblog.php 或user/blog.php  载入方法$this->load->view("userblog");或
        $this->load->view("user/blog");






    


    



             