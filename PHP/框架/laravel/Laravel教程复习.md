### web 开发实战

##### laravel 解决浏览器缓存问题

laravel-mix 是 laravel 提供的一个前端解析工具

```javascript
cosnt mix = require('laravel-mix')

mix.js('resources/js/app.js','public/js')
	.sass('resources/sass/app.css','public/css').version()

# 使用了 version() 函数，在模板引用 css 时，必须使用 mix() 方法
```

```php
Route::resource('users','UsersController');
# 表示以下路由
Route::get('/users','UsersController@index')->name('users.index'); # 显示所有用户的列表页面
Route::get('/users/create','UsersController@create')->name('users.create'); # 创建用户页面
Route::get('/users/{user}','UsersController@show')
->name('users.show'); # 显示用户个人信息
Route::post('/users','UsersController@store')->name('users.store'); # 创建用户
Route::get('/users/{user}/edit','UsersController@edit')->name('users.edit'); # 编辑用户个人资料页面
Route::patch('/users/{user}','UsersController@update')->name('users.update'); # 更新用户
Route::delete('/users/{user}','UsersController@destory')->name('users.destory'); # 删除用户

```

laravel 提供全局辅助函数 `old` 帮助在 blade 模板显示旧输入数据，这样当信息填写错误，页面进行重定向访问时，输入框将自动填写上最后一次输入过的数据

`{{ csrf_field() }}` 可以防止 post 提交受到 CSRF (跨站请求伪造)

> laravel 默认会将所有的验证信息进行闪存，当检测到错误存在时，laravel 会自动将这些错误消息绑定到视图上，因此可以在所有视图上使用  `errors` 变量来显示错误信息，Tips：使用 `errors` 变量时 先使用 `count($errors)` 检查其值是否为空

```php
redirect()->route('users.show',[$user]);
// 此处这样的写法，是 laravel 的 route() 方法会自动获取 model 的主键，等同于以下
redirect()->route('users.show',[$user->id]);
```

由于 HTTP 协议是无状态的，laravel 提供了一种用于临时保存用户数据的方法 session ，并附带了支持多种会话后端驱动，可通过同意的 API 使用

使用 `session()` 方法访问会话实例，当想存入一条缓存数据，让它只在下一次请求内有效是，可以使用 `flash` 方法

```php
session()->flash('success','欢迎，您将在这里开启一段新的旅程');
# 取出方法
session()->get('success');
```

 laravel 提供了 `Auth` 的 `attempt` 方法可以很方便的完成用户的身份认证，详细的用户认证查看 [laravel -用户认证章节](https://learnku.com/docs/laravel/5.7/authentication/2269)

```php
return redirect()->back()->withInput();
# 使用 withInput() 后模板里的 old() 方法将能获取到上一次用户提交的内容
```

`method_field('DELETE')`  由于浏览器不支持发送 DELETE 请求，而 RESTful 架构中会使用 DELETE 请求来删除一个资源，所以使用了隐藏域来伪造 DELETE 请求

```html
<input type="hidden" name="_method" value="DELETE">
```

laravel 默认配置中，如果用户登录后没有使用记住我功能，则登录状态只会被记住两个小时