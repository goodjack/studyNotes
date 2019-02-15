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



