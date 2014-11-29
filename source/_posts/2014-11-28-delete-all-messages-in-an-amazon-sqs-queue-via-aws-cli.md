title: Delete All Messages in an Amazon SQS Queue via AWS CLI
date: 2014-11-28 12:00:00
categories: [Tools]
tags: [amazon-sqs, aws-cli, queue, jq, cli, aws, amazon]
---

[Amazon SQS] or Simple Queue Service is a fast, reliable, scalable, fully managed message queuing service. There is also [AWS CLI] or Command Line Interface available to use with the service.

If you have a lot of messages in a queue, this command will show the approximate number:

```sh
$ aws sqs get-queue-attributes \
  --queue-url $url \
  --attribute-names ApproximateNumberOfMessages
```

Where `$url` is the URL to the Amazon SQS queue.

There is no command to delete all messages yet, but you can chain a few commands together to make it work:

<!-- more -->

```sh
$ while true; do \
    aws sqs receive-message --queue-url $url --max-number-of-messages 10 \
    | jq -c -M \ 
    '.Messages[] | "Id=" + .MessageId + ",ReceiptHandle=" + .ReceiptHandle' \
    | xargs aws sqs delete-message-batch --queue-url $url --entries || break; \
  done
```

Amazon SQS allows you to receive up to 10 messages at most, so you have to loop through it. The default response of the `receive-message` command is in JSON, hence using `jq` to process the result and extract just what we need for deletion: `MessageId` and `ReceiptHandle`.

If there are no more messages, you will likely get the following error:

```plain
list index out of range
xargs: aws: exited with status 255; aborting
```

Then you exit the loop.

With the network latency and limitation of 10 messages at a batch, this is pretty slow. But you can run multiple commands to speed up the deletion process. Or take the shortcut with a faster way: delete the entire queue and then recreate it.

[Amazon SQS]: http://aws.amazon.com/sqs/
[AWS CLI]: http://aws.amazon.com/cli/
