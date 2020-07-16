# CodeIgniter Framework


# 安裝 CodeIgniter 環境

1. 先至[CodeIgniter](https://codeigniter.org.tw/userguide3/installation/downloads.html)下載安裝檔 <br>

2. `將CodeIgniter解壓縮` > `將檔案放置` > `XAMPP` > `htdocs` > `CodeIgniter`

3. `開啟 XAMPP` > `啟動server` > `開啟瀏覽器` > http://localhost/codeigniter/index.php `(輸入CodeIgniter放置路徑)` > 即可看到預設畫面


# CodeIgniter 教學

## 新增靜態頁面

### 新增 pages.php 檔案

`路徑 application/controllers/pages.php`

code:
```php
<?php
  class pages extends CI_Controller {
    public function view($page = 'home') {
           if (!file_exists(APPPATH.'views/pages/'.$pages.'.php')) {
               //Whoops, we don't have a page for that !
               show_404();`
           }`
           $data['title'] = ucfirst($pages);  //Capitalize the first letter

           $this->load->view('templates/header', $data);
           $this->load->view('pages/'.$page, $data);
           $this->load->view('templates/footer', $data);
       }
   }
?>
```


### 新增 header.php 檔案

`路徑 application/views/templates/header.php`

code:
```php
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>CodeIgniter Tutorial</title>
    </head>
    <body>
        <h1><?php echo $title; ?></h1>
```


### 新增 footer.php 檔案

`路徑 application/views/templates/footer.php` 

code:
```php
        <em>&copy; 2020 | All Rights Reserved. Designed by Sky</em>
    </body>
</html>
```

### 新增 home.php 檔案

`路徑 application/views/pages/home.php` 

code:
```php
<?php echo "This is home page"; ?><br />
```

### 新增 about.php 檔案

`路徑 application/views/pages/about.php` 

code:
```php
<?php echo "This is about page"; ?><br />
```


開啟瀏覽器輸入URL:`http://localhost/codeigniter/index.php/pages/view`

自訂的路由規則 `http://example.com/[controller-class]/[controller-method]/[arguments]`


### 修改路由
`路徑 application/config/routes.php`

code:

```php
<?php
defined('BASEPATH') OR exit('No direct script access allowed');

$route['default_controller'] = 'pages/view';
$route['(:any)'] = 'pages/view/$1';
```

開啟瀏覽器輸入URL:`http://localhost/codeigniter/index.php/about`
