# Docs

در اینجا یک روتر درست کردیم واسه حذف کردن یک درخواست بعد 5 دقیقه
<br/>

```php
Route::delete('/delete/{id}', 'ApplicationController@delete_all');
```
<br/>
اول از همه ایدی درخواست رو از ریکوِِیست میگیریم
<br/>
تایم زون رو روی تهران ست میکنیم

```php
public function delete_all(Request $request, int $id)
    {
        // find applications insert time
        $applications = Application::where("id", $id)->first();

        // set timezon
        date_default_timezone_set("Asia/Tehran");

        $insert_time = strtotime($applications->created_at->toarray()['formatted']) + 300;
        $now = strtotime(date("Y-m-d H:i:s"));

        // user can delete record after 5 minutes 
        if ($insert_time <= $now) {
            return response(["status" => 400, 'message' => "cant delete record..."]);
        }

        // deleted record
        Application::where('id', $id)->delete();
        ApplicationNotification::where('application_id', $id)->delete();
        ApplicationImage::where("application_id", $id)->delete();

        return response(["status" => 200,  "message" => "record deleted..."]);
    }
```