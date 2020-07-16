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



## 新增動態模組

### 設定 Model (模型) 
`路徑 application/models/News_model.php`

code:

```php
<?php
    class news_model extends CI_Model {
        public function __construct() {
            $this->load->database();
        }

        public function get_news($slug = FALSE) {
            if ($slug === FALSE) {
                $query = $this->db->get('news');
                return $query->result_array();
            }

            $query = $this->db->get_where('news', array('slug' => $slug));
            return $query->row_array();
        }
    }
?>
```

### 新增資料庫
#### 開啟XAMPP 啟動 MySQL database server
`路徑 http://localhost/phpmyadmin/server_sql.php`

> 點選 SQL 新增資料庫
```sql
    CREATE database CI_news
```

> 點選 SQL 新增資料欄位
```SQL
    CREATE TABLE news (
        id int(11) NOT NULL AUTO_INCREMENT,
        title varchar(128) NOT NULL,
        slug varchar(128) NOT NULL,
        text text NOT NULL,
        PRIMARY KEY (id),
        KEY slug (slug)
    );
```

> 點選 SQL 加入2筆資料
``` SQL
    INSERT INTO `CI_news`.`news` (`id`, `title`, `slug`, `text`) VALUES (NULL, 'Write Like You Talk', 'false', 'Here\'s a simple trick for getting more people to read what you write: write in spoken language. Something comes over most people when they start writing. They write in a different language than they\'d use if they were talking to a friend. The sentence structure and even the words are different. No one uses "pen" as a verb in spoken English. You\'d feel like an idiot using "pen" instead of "write" in a conversation with a friend.');
    INSERT INTO `CI_news`.`news` (`id`, `title`, `slug`, `text`) VALUES (NULL, 'A decade at google', 'true', 'One of the key challenges you face in an industrial research lab is how to choose your projects. You want your projects to be interesting research but also contribute to your company. As a junior researcher, you’re typically in the situation of choosing a project to join, while later in your career you are expected to come up with and lead your own projects. Regardless of your age, you have to make an educated decision.');
```

### 顯示資料庫資料

#### 取回資料
`路徑 application/controllers/news.php`

code:

```php
<?php
    class news extends CI_Controller {

        public function __construct() {
            parent::__construct();
            $this->load->model('news_model');
        }

        public function index() {
            $data['news'] = $this->news_model->get_news();
            $data['title'] = 'News archive';

            $this->load->view('templates/header', $data);
            $this->load->view('news/index', $data);
            $this->load->view('templates/footer');
        }

        public function view($slug = NULL) {
            $data['news_item'] = $this->news_model->get_news($slug);
        
            if (empty($data['news_item'])) {
                show_404();
            }

            $data['title'] = $data['news_item']['title'];

            $this->load->view('templates/header', $data);
            $this->load->view('news/view', $data);
            $this->load->view('templates/footer');
        }
    }
?>
```
#### 顯示資料

`路徑 application/controllers/news.php`

code:

```php
<h2><?php echo $title ?></h2>

<?php foreach ($news as $news_item): ?>

        <h3><?php echo $news_item['title'] ?></h3>
        <div class="main">
                <?php echo $news_item['text'] ?>
        </div>
        <p><a href="news/<?php echo $news_item['slug'] ?>">View article</a></p>

<?php endforeach ?>
```

#### 建立檢視資料頁面

`路徑 application/views/news/view.php`

code:

```php
<?php
    echo '<h2>'.$news_item['title'].'</h2>';
    echo $news_item['text'];
```

### 修改路由
`路徑 application/config/routes.php`

code:

```php
<?php
defined('BASEPATH') OR exit('No direct script access allowed');

$route['news/(:any)'] = 'news/view/$1';
$route['news'] = 'news';
$route['(:any)'] = 'pages/view/$1';
$route['default_controller'] = 'pages/view';
```

開啟瀏覽器輸入URL:`http://localhost/codeigniter/index.php/about`

### 修改連結資料庫

`路徑 application/config/database.php`

code:

``` php
    $db['default'] = array(
	'dsn'	=> '',
	'hostname' => 'localhost',
	'username' => 'your user name',
	'password' => 'your password',
	'database' => 'CI_news',
	'dbdriver' => 'mysqli',

);
```

開啟瀏覽器輸入URL:`http://localhost/codeigniter/index.php/news`


## 新增資料到資料庫

### 建立表單頁面

`路徑 application/views/news/create.php`

code:

```php
<h2><?php echo $title ?></h2>

<?php echo validation_errors(); ?>

<?php echo form_open('news/create') ?>

    <label for="title">Title</label>
    <input type="input" name="title" /><br />

    <label for="text">Text</label>
    <textarea name="text"></textarea><br />

    <input type="submit" name="submit" value="Create news item" />

</form>
```

### 建立、驗證表單
`路徑 application/controllers/news.php`

code:

```php
<?php
    class news extends CI_Controller {
        
        public function __construct() {
            parent::__construct();
            $this->load->model('news_model');
        }

        public function index() {
            $data['news'] = $this->news_model->get_news();
            $data['title'] = 'News archive';

            $this->load->view('templates/header', $data);
            $this->load->view('news/index', $data);
            $this->load->view('templates/footer');
        }

        public function view($slug = NULL) {
            $data['news_item'] = $this->news_model->get_news($slug);
        
            if (empty($data['news_item'])) {
                show_404();
            }

            $data['title'] = $data['news_item']['title'];

            $this->load->view('templates/header', $data);
            $this->load->view('news/view', $data);
            $this->load->view('templates/footer');
        }

        // 驗證表單，建立表單
        public function create() {
            $this->load->helper('form');
            $this->load->library('form_validation');

            $data['title'] = 'Create a news item';

            $this->form_validation->set_rules('title', 'Title', 'required');
            $this->form_validation->set_rules('text', 'text', 'required');

            if ($this->form_validation->run() === FALSE) {
                $this->load->view('templates/header', $data);
                $this->load->view('news/create');
                $this->load->view('templates/footer');
            } else {
                $this->news_model->set_news();
                $this->load->view('news/success');
            }
        }
    }
?>
```

### 將資料存進資料庫
`路徑 application/models/News_model.php`

code:

```php
<?php
    class news_model extends CI_Model {
        public function __construct() {
            $this->load->database();
        }

        public function get_news($slug = FALSE) {
            if ($slug === FALSE) {
                $query = $this->db->get('news');
                return $query->result_array();
            }

            $query = $this->db->get_where('news', array('slug' => $slug));
            return $query->row_array();
        }

        public function set_news() {
            $this->load->helper('url');

            $slug = url_title($this->input->post('title'), 'dash', TRUE);

            $data = array(
                'title' => $this->input->post('title'),
                'slug' => $slug,
                'text' => $this->input->post('text')
            );

            return $this->db->insert('news', $data);
        }
    }
?>
```
### 建立成功頁面
`路徑 application/views/news/success.php`

code:

```php
    <?php echo "SUCCESS OK!!!" ?>
```

### 修改路由
`路徑 application/config/routes.php`

code:

```php
<?php
defined('BASEPATH') OR exit('No direct script access allowed');

$route['news/create'] = 'news/create';
$route['news/(:any)'] = 'news/view/$1';
$route['news'] = 'news';
$route['(:any)'] = 'pages/view/$1';
$route['default_controller'] = 'pages/view';
```

開啟瀏覽器輸入URL:`http://localhost/codeigniter/index.php/about`
