
## For PHP(Laravel  Project)
```bash
php artisan config:clear
php artisan config:cache
php artisan tinker --execute="dump(in_array('9876543210', config('otp.bypass_numbers', []), true));"
```