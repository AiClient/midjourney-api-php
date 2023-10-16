# midjourney-api-php
Use Midjourney and call the imagine command with our API in PHP

## Step 1 – Generate a bearer token
[Login](https://app.ai-client.com/) and then create an application from the [My account menu](https://app.ai-client.com/account).

Generate a bearer token from your new application.

[Bearer-token-creation-aiclient.webm](https://github.com/AiClient/midjourney-api-php/assets/147600871/706a23d3-b852-4c52-b0b7-bfbde53b0205)

## Step 2 – Start a new thread with Midjourney
You may select an **ai_version_code**. For example : **midjourney**.

Send an http POST request to create a new thread.

```php
<?php
$bearer = '<<your_token_here>>';

// CREATE THREAD
// -------------
$url = "https://app.ai-client.com/api/v1/threads";

$headers = [
  'Accept: application/json',
  'Authorization: Bearer '.$bearer
];

$options = [
    'ai_version_code' => 'midjourney'
];

$curl = curl_init();
curl_setopt_array($curl, array(
  CURLOPT_URL => $url,
  CURLOPT_CUSTOMREQUEST => 'POST',
  CURLOPT_POSTFIELDS => $options,
  CURLOPT_HTTPHEADER => $headers,
  CURLOPT_RETURNTRANSFER => true
));

$response = curl_exec($curl);
$thread = json_decode($response)->data;
curl_close($curl);

print_r($thread);
```

This will return :

```
stdClass Object
(
    [guid] => 268e4bc4-89e2-441b-9182-afcd288a325b
    [ai_code] => midjourney
    [ai_version_code] => midjourney
    [title] => New thread with Midjourney
    [created_at] => 2023-06-16T19:55:04.000000Z
    [updated_at] => 2023-06-16T19:55:04.000000Z
)
```

## Step 3 – Create a thread entry to send a message
You may use the **thread** object to create a new entry with a **POST** request.

```php
$url = "https://app.ai-client.com/api/v1/threads/" . $thread->guid . "/entry";

$headers = [
  'Accept: application/json',
  'Authorization: Bearer '.$bearer
];

$options = ['type' => 'request', 'command' => 'imagine', 'prompt' => "Cats and dogs with lightsabers --ar 16:9"];

$curl = curl_init();
curl_setopt_array($curl, array(
  CURLOPT_URL => $url,
  CURLOPT_CUSTOMREQUEST => 'POST',
  CURLOPT_POSTFIELDS => $options,
  CURLOPT_HTTPHEADER => $headers,
  CURLOPT_RETURNTRANSFER => true
));

$response = curl_exec($curl);
$threadEntry = json_decode($response)->data;
curl_close($curl);

print_r($threadEntry);
```

This will return :

```
stdClass Object
(
    [guid] => a83317fc-8675-4adf-abfa-d1397d3b7af6
    [thread_guid] => 268e4bc4-89e2-441b-9182-afcd288a325b
    [type] => request
    [error] =>
    [content] => stdClass Object
        (
            [command] => imagine
            [prompt] => Cats and dogs with lightsabers --ar 16:9
        )

    [waiting_response] => 1
    [credits] => 0
    [created_at] => 2023-06-16T19:55:04.000000Z
)
```

## Step 4 – Wait for a response from Midjourney

You must now wait for the AI’s response. The “**waiting_response**” property of your thread entry informs you of Midjourney’s response.

You may use the **thread** object to get all the thread entries. Send a **GET** request. You will see your thread entry of type “**request**” and the Midjourney thread entry of type “**response**“.

```php
$url = "https://app.ai-client.com/api/v1/threads/" . $thread->guid . "/entries";

$headers = [
  'Accept: application/json',
  'Authorization: Bearer '.$bearer
];

$curl = curl_init();
curl_setopt_array($curl, array(
  CURLOPT_URL => $url,
  CURLOPT_CUSTOMREQUEST => 'GET',
  CURLOPT_HTTPHEADER => $headers,
  CURLOPT_RETURNTRANSFER => true
));

$response = curl_exec($curl);
$threadEntries = json_decode($response)->data;
curl_close($curl);

print_r($threadEntries);
```

This will return :


```
Array
(
    [0] => stdClass Object
        (
            [guid] => a83317fc-8675-4adf-abfa-d1397d3b7af6
            [thread_guid] => 268e4bc4-89e2-441b-9182-afcd288a325b
            [type] => request
            [error] =>
            [content] => stdClass Object
                (
                    [command] => imagine
                    [prompt] => Cats and dogs with lightsabers --ar 16:9
                )

            [waiting_response] =>
            [credits] => 0
            [created_at] => 2023-06-16T19:55:04.000000Z
        )

    [1] => stdClass Object
        (
            [guid] => 06b89e01-2aaa-4406-a310-d4e2781e0534
            [thread_guid] => 268e4bc4-89e2-441b-9182-afcd288a325b
            [type] => response
            [error] =>
            [content] => stdClass Object
                (
                    [state] => completed
                    [percent] => 100
                    [images] => Array
                        (
                            [0] => stdClass Object
                                (
                                    [url] => https://app.ai-client.com/storage/midjourney/43ed663c-88fd-4afd-9143-d057f61f2cdb.png
                                    [thumbnail_url] => https://app.ai-client.com/storage/midjourney/43ed663c-88fd-4afd-9143-d057f61f2cdb.png.thumbnail.jpg
                                    [dimensions] => 1456x816
                                    [filesize] => 1457224
                                )

                            [1] => stdClass Object
                                (
                                    [url] => https://app.ai-client.com/storage/midjourney/92115483-f465-4226-bd87-9c12907aad1c.png
                                    [thumbnail_url] => https://app.ai-client.com/storage/midjourney/92115483-f465-4226-bd87-9c12907aad1c.png.thumbnail.jpg
                                    [dimensions] => 1456x816
                                    [filesize] => 1539160
                                )

                            [2] => stdClass Object
                                (
                                    [url] => https://app.ai-client.com/storage/midjourney/8ddceaee-6204-41d5-95ec-ce48877d8461.png
                                    [thumbnail_url] => https://app.ai-client.com/storage/midjourney/8ddceaee-6204-41d5-95ec-ce48877d8461.png.thumbnail.jpg
                                    [dimensions] => 1456x816
                                    [filesize] => 1392490
                                )

                            [3] => stdClass Object
                                (
                                    [url] => https://app.ai-client.com/storage/midjourney/c95d9f08-f5ee-4470-be45-9cb602984f61.png
                                    [thumbnail_url] => https://app.ai-client.com/storage/midjourney/c95d9f08-f5ee-4470-be45-9cb602984f61.png.thumbnail.jpg
                                    [dimensions] => 1456x816
                                    [filesize] => 1656974
                                )

                        )

                )

            [waiting_response] =>
            [credits] => 10
            [created_at] => 2023-06-16T19:55:07.000000Z
        )

)
```
