##### 用户认证

`php artisan make:auth`

##### 邮箱认证

```php
use Illuminate\Contracts\Auth\MustVerifyEmail as MustVerifyEmailContract; // 作为接口类继承此类确保 User 遵守规范
use Illuminate\Auth\MustVerifyEmail as MustVerifyEmailTrait;
Class User extends Controller implements MustVerifyEmailContract
{
    use MustVerifyEmailTrait;
    // 该 trait 包含三个方法
    // hasVerifiedEmail 检测用户 email 是否认证
    // MarkEmailAsVerified 将用户标示为已认证
    // sendEmailVerificationNotification 发送 Email 认证消息通知，触发邮件的发送
}
```

###### 新建话题 观察者模式

观察者类的方法名反映 Eloquent 想监听的事件，每个方法接受 model 作为唯一参数

```php
php artisan make:observer UserObserver --model=User
```

在 `AppServiceProvider` 注册观察者

```php
class AppServiceProvider extends ServiceProvider
{
    public function boot()
    {
        User::observer(UserObserver::class);
    }
}
```



