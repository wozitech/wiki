<!-- TITLE: wit-myBuses -->
<!-- SUBTITLE: An Alexa skill to tell me when the next bus is due for my given destinations -->

# Overview
A personalised Alexa skill, called `myNextBus` that calls upon an AWS Lambda function, maintained and deployed using `serverless framework`. Calls upon TFL's public API, https://api-portal.tfl.gov.uk, using Axios, passing an `API App ID` and `API App Key` generated from my account; the details of which are held in an AWS Secret Manager resource. The IAM role assumed by the lambda is managed by Terraform; granting access to the `TFL_API_Portal` SecretManager resource only.
# Assumptions
Assumes:
* A fixed set of locations as known from my house, e.g. Brixton, Clapham.
	* These destinations is a `slot type` and a given list of `slot values` defined with the skill.
* That fixed set of destinations and paired with a given source, based on the known TFL 'stop point' for a preferred bus route.
* Each known source has a given 'travel time'; that is the time it takes to walk from my house to the bus stop. This is used when setting a reminder.

# Alexa Skill
This skill is to be invoked with utterances such as:
* Alexa, when is my next bus to Clapham?  -> Alexa will return with the expected time for up to three buses.
* Alexa, how long until my next bus to Croydon? -> Alexa will return with the number of minutes.

This skill has a follow on to the first question, that being:
* Would you like me to remind you?

The skill is named: `myNextBus`.
The invocation name is: `my next bus`.
The skill id: `amzn1.ask.skill.2ba78764-0a67-481f-907a-3f7c08287aeb`.

Other than the default 'built-in' intents, one new intent called `whenIsNextBus`; this has a single utterance: `when is my next bus to {Destination}`. Another intent called `howLongUntil`; this has a single utterance: `how long until my next bus to {Destination}`.

`{Destination}` is the slot; this is a list of known destinations (known to me):
* Brixton
* Clapham
* Croydon
* Crystal Palace
* ...

The `{Destination}` slot is marked as required within `Slot Filling`. If not given, Alexa will prompt; this applied to both `whenIsNextBus` and `howLongUntil` intents. For example:
* I ask: Alexa, when is my next bus?
	* Akexa responds with: To which destination?
		* I say: Clapham.

> The Alexa Skills builds, but it fails on test for both intents.

```
{
    "interactionModel": {
        "languageModel": {
            "invocationName": "my next bus",
            "intents": [
                {
                    "name": "AMAZON.FallbackIntent",
                    "samples": []
                },
                {
                    "name": "AMAZON.CancelIntent",
                    "samples": []
                },
                {
                    "name": "AMAZON.HelpIntent",
                    "samples": []
                },
                {
                    "name": "AMAZON.StopIntent",
                    "samples": []
                },
                {
                    "name": "AMAZON.NavigateHomeIntent",
                    "samples": []
                },
                {
                    "name": "whenIsNextBus",
                    "slots": [
                        {
                            "name": "Destination",
                            "type": "LIST_OF_DESTINATIONS"
                        }
                    ],
                    "samples": [
                        "when is my next bus to {Destination}"
                    ]
                },
                {
                    "name": "howLongUntil",
                    "slots": [
                        {
                            "name": "Destination",
                            "type": "LIST_OF_DESTINATIONS"
                        }
                    ],
                    "samples": [
                        "how long until my next bus to {Destination}"
                    ]
                }
            ],
            "types": [
                {
                    "name": "LIST_OF_DESTINATIONS",
                    "values": [
                        {
                            "name": {
                                "value": "Clapham"
                            }
                        },
                        {
                            "name": {
                                "value": "Brixton"
                            }
                        },
                        {
                            "name": {
                                "value": "Streatham"
                            }
                        },
                        {
                            "name": {
                                "value": "Croydon"
                            }
                        },
                        {
                            "name": {
                                "value": "Norwood Junction"
                            }
                        }
                    ]
                }
            ]
        },
        "dialog": {
            "intents": [
                {
                    "name": "whenIsNextBus",
                    "confirmationRequired": true,
                    "prompts": {
                        "confirmation": "Confirm.Intent.423052006003"
                    },
                    "slots": [
                        {
                            "name": "Destination",
                            "type": "LIST_OF_DESTINATIONS",
                            "confirmationRequired": false,
                            "elicitationRequired": true,
                            "prompts": {
                                "elicitation": "Confirm.Intent.423052006003"
                            }
                        }
                    ]
                },
                {
                    "name": "howLongUntil",
                    "confirmationRequired": true,
                    "prompts": {
                        "confirmation": "Confirm.Intent.423052006003"
                    },
                    "slots": [
                        {
                            "name": "Destination",
                            "type": "LIST_OF_DESTINATIONS",
                            "confirmationRequired": false,
                            "elicitationRequired": true,
                            "prompts": {
                                "elicitation": "Confirm.Intent.423052006003"
                            }
                        }
                    ]
                }
            ]
        },
        "prompts": [
            {
                "id": "Confirm.Intent.423052006003",
                "variations": [
                    {
                        "type": "PlainText",
                        "value": "To which destination?"
                    }
                ]
            }
        ]
    }
}
```

# Lambda Function
The lambda function is described and deployed using `serverless framework`:
```
functions:
  myBuses:
    handler: myBuses.handler
    environment:
      TFL_API_SECRET_ID: 'TFL_API_Portal'
    role: arn:aws:iam::#{AWS::AccountId}:role/WOZiTech_eu_west_1_tfl_lambda_role
		events:
      - alexaSkill: amzn1.ask.skill.2ba78764-0a67-481f-907a-3f7c08287aeb
    tags:
      application: "wit-home"
      company: "WOZiTech"
    package:
      include:
        - myBuses.js

```

It triggers (events) from an `alexaSkill`; the resulting CloudFormation stack on deploy includes setting up permissions on AlexaSkill to invoke lambda for the given skill; `amzn1.ask.skill.2ba78764-0a67-481f-907a-3f7c08287aeb`. This ensures the lambda function can only be called by my skill.

The `TFL_API_SECRET_ID` is simply the AWS Secrets Manager resource name, which is used within the lambda function to fetch the TFL API app id/app key. The role `WOZiTech_eu_west_1_tfl_lambda_role` grants permission to this lambda function to look up the secret; the IAM role is maintained with Terraform and WOZ*iTech* preferred practice is to restrict roles to operate within an given region:
```
        {
            "Effect": "Allow",
            "Action": [
              "secretsmanager:DescribeSecret",
              "secretsmanager:GetSecretValue"
            ],
            "Resource": "arn:aws:secretsmanager:${var.region}:${var.account}:secret:TFL_API_Portal-jT6jsf"
        }
```

The `...tfl_lambda_role` role is attributed to "eu-west-1", rather than "eu-west_2" because Alexa Skill triggers not available in "eu-west-2".
# TODO
* A customised source; currently assumes 'my house'. Should locate the "tfl stop points" nearest to the location of the given Alexa when the skill is added. Allow for the 'Alexa location' to be overriden.
* A customised set of destinations; currently assumes 'my given destinations' only. For each source, show the lines that run through that source, allowing a 'destination' to be assigned.