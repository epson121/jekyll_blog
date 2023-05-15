---
layout: post
title: Stram large video file in Symfony
date: 2023-05-15 10:00:00.000000000 +01:00
author: luka
categories:
- PHP
- Symfony
---

Streaming a large video file from your Symfony application is an interesting problem. We need to be able to serve data to the client continuously, as well as make sure memory footprint on server is not causing issues. Let's take a look at a simple approach to solving this.

I'm assuming we have a Symfony application, and a controller already set up. Also, for convenience, a video file already exists, and is accessible to the server. In order to show the video on the frontend, some HTML is required (with Twig templating):

{% raw %}

    <video width="320" height="240" controls src="{{ path('app_stream_large') }}"></video>

{% endraw %}
We need a `video` element, with source pointing to our controller name (Symfony controller name).

Serve that HTML in a separate controller:

```php
    #[Route('/watch', name: 'app_watch')]
    public function watch()
    {
        $response = $this->render('watch.html.twig');

        return $response;
    }
```

Now for the streaming controller:

```php

    // FQN imports
    // use Symfony\Component\HttpFoundation\StreamedResponse;
    // use Symfony\Component\HttpFoundation\ResponseHeaderBag;
    // use Symfony\Component\DependencyInjection\ParameterBag\ContainerBagInterface;

    #[Route('/stream_large', name: 'app_stream_large')]
    public function stream(ContainerBagInterface $containerBagInterface)
    {
        # get the movie path
        $path = $containerBagInterface->get('kernel.project_dir');
        $path = "$path/public/files/mov.mkv";

        # create a StreamedResponse instance
        $response = new StreamedResponse();

        # set callback for StreamedResponse - this will be called to serve the content
        # anything echoed out in this function is sent to the client
        $response->setCallback(function() use ($path) {
            $this->readfile_chunked($path);
        });

        return $response;
    }
```

`readfile_chunked` is a function I found on PHP documentation page: https://www.php.net/manual/en/function.readfile.php#54295

```php
    // Read a file and display its content chunk by chunk
    public function readfile_chunked($filename, $retbytes = TRUE) {
        $buffer = '';
        $cnt    = 0;
        $handle = fopen($filename, 'rb');

        if ($handle === false) {
            return false;
        }

        ob_start();

        while (!feof($handle)) {
            $buffer = fread($handle, 1024*1024);
            echo $buffer;
            ob_flush();
            flush();

            if ($retbytes) {
                $cnt += strlen($buffer);
            }
        }

        $status = fclose($handle);

        if ($retbytes && $status) {
            return $cnt; // return num. bytes delivered like readfile() does.
        }

        return $status;
    }
```

To sum up, we open the `/watch` endpoint, which serves the `video` HTML tag. Source for the video is our second controller `/stream_large`, streaming the content in pieces, when asked for (by pressing play in the video UI on frontend).

Nice thing to add to this would be proper controls for video element, as currently only `play` and `pause` work out of the box.


> Wait, I’m having one of those things. You know, a headache with pictures.
>
> -- <cite>Phillip J. Fry</cite>

