```
                   V
                  /'>>>
                 /*/
                / /
               /*/
              / /
      -------/*/   _____ _____ _____ _____ _____    __    __    _____ _____ _____
   --/  *  * */   |     |  |  |  _  |     |   __|  |  |  |  |  |  _  |     |  _  |
    /* * *  */    |   --|     |     |  |  |__   |  |  |__|  |__|     | | | |     |
    -  --- -/     |_____|__|__|__|__|_____|_____|  |_____|_____|__|__|_|_|_|__|__|
     H    H
     H    H
     --   --
```

- **Website**: [https://llamakit.com](http://llamakit.com)
- **Source**: [https://github.com/hassy/llama-cli](https://github.com/hassy/llama-cli)
- **Issues**: [https://github.com/hassy/llama-cli/issues](https://github.com/hassy/llama-cli/issues)
- **Twitter**: [@hveldstra](https://twitter.com/hveldstra)

# Meet Chaos Llama

Chaos Llama will wreak havoc* on your AWS infrastructure to help you build systems that are lean, mean, and resilient to failure.

<sub>* - in a controlled manner, Llama is there to serve you and would never go rogue</sub>

# About

Chaos Llama is a small tool for testing resiliency and recoverability of AWS-based architectures. Once configured and deployed, it will randomly terminate or otherwise interfere<sup>**[*](#features)**</sup> with the operation of your EC2 instances and ECS tasks. It is inspired by Netflix's [Chaos Monkey](https://github.com/Netflix/SimianArmy/wiki/Chaos-Monkey), but instead of requiring an EC2 instance to run on, it uses AWS Lambda. Think of it as Chaos Monkey rebuilt with 2016 tech.

## Installation

```shell
npm install -g llama-cli
```

## Setting Up
### AWS Configuration

An IAM user and a role for the lambda need to be set up first.

#### IAM User

Must be set up and credentials set up in `~/.aws/credentials`

#### Lambda Role

Required policies:
- AmazonEC2FullAccess

### Setting up Chaos Llama

To create the AWS Lambda function run:

```shell
llama deploy -r $lambda-role-arn
```

This will create a state file (`llama_config.json`) which is needed for
subsequent re-deploys, and deploy Chaos Llama to AWS. Llama will be configured
to run once an hour, but it **won't do anything** every time it runs.

To configure termination rules, run `deploy` with a [`Llamafile`](./Llamafile.json):

```shell
llama deploy -c Llamafile.json
```

#### Llamafile.json

Example Llamafile:

```javascript
{
  "interval": "60",
  "enableForASGs": [
  ],
  "disableForASGs": [
  ]
}
```

**Options:**

- `interval` (in minutes) - how frequently Chaos Llama should run. Minimum
value is `5`. Default value is `60`.
- `enableForASGs` - whitelist of names of ASGs to pick an instance from.
Instances in other ASGs will be left alone. Empty list (`[]`) means Chaos Llama
won't do anything.
- `disableForASGs` - names of ASGs that should not be touched; instances in any
other ASG are eligible for termination.

If both `enableForASGs` and `disableForASGs` are specified, then only
`enableForASGs` rules are applied.

## Chaos Llama vs Chaos Monkey

Chaos Llama is inspired by Netflix’s <a href="https://github.com/Netflix/SimianArmy/wiki/Chaos-Monkey">Chaos Monkey</a>. Curious about the differences? Here’s a handy summary:

| Llama           | Monkey  |
|:-------------|:-----|
| Serverless (runs on AWS Lambda) - no maintenance | Needs EC2 instances to run on |
| Extremely easy to deploy      | Needs quite a bit of setup and config ([&raquo;&raquo;&raquo;](https://github.com/Netflix/SimianArmy/wiki/Quick-Start-Guide)) |
| Small codebase, easy to understand and extend (<400 SLOC)      | Large codebase (thousands of SLOC) |
| Written in JS | Written in Java |
| New on the scene | Mature project |
| Small featureset | Many features |
| ECS support in the works | Does not support ECS |
| Open source under MPL 2.0 / MIT | Open source under APL 2.0 |
| Developed by [Shoreditch Ops](https://twitter.com/ShoreditchOps) | Developed by Netflix |


## Why Use Chaos Llama?

> Failures happen, and they inevitably happen when least desired. If your application can't tolerate a system failure would you rather find out by being paged at 3am or after you are in the office having already had your morning coffee? Even if you are confident that your architecture can tolerate a system failure, are you sure it will still be able to next week, how about next month? Software is complex and dynamic, that "simple fix" you put in place last week could have undesired consequences. Do your traffic load balancers correctly detect and route requests around system failures? Can you reliably rebuild your systems? Perhaps an engineer "quick patched" a live system last week and forgot to commit the changes to your source repository?

(source: [Chaos Monkey wiki](https://github.com/Netflix/SimianArmy/wiki/Chaos-Monkey#why-run-chaos-monkey))

Further reading: [Principles Of Chaos Engineering](http://principlesofchaos.org)

## Current Limitations

### Supported AWS Regions

Chaos Llama will only work in these regions (due to a limitation with AWS Lambda Schedules):

- US East (Northern Virginia)
- US West (Oregon)
- Europe (Ireland)
- Asia Pacific (Tokyo)

### Features

Right now, Chaos Llama only knows how to terminate instances and does not support more advanced interference modes, like introducing extra latency (but it's on the roadmap and being worked on, see [Issue #4](https://github.com/hassy/llama-cli/issues/4)).

## Support

File an [issue](https://github.com/hassy/llama-cli/issues) or drop me a line on [h@veldstra.org](mailto:h@veldstra.org). Enterprise support and consulting is available through [Shoreditch Ops](https://artillery.io/enterprise.html).

## License

MPL 2.0 - see [LICENSE.txt](./LICENSE.txt) for details.

The [lambda/index.js](./lambda/index.js) file is dual-licensed under MPL 2.0 and MIT and can be used under the terms of either of those licenses.

---

<sub>A project by [Shoreditch Ops](https://twitter.com/ShoreditchOps), creators of [artillery.io](https://artillery.io) ⚡️ - simple &amp; powerful load-testing framework.</sub>
