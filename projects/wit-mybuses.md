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
* Alexa, when is **my next bus** to Clapham?  -> Alexa will return with the expected time for up to three buses.
* Alexa, how long until **my next bus** to Croydon? -> Alexa will return with the number of minutes.

Turns out, to invoke a custom skill, you need to invoke the skill with a name and with the verbs such as, tell and ask. So rather than being able to use "my next bus" as the invocation name, I've decided to use one of my cats name (the chatty one, "Becks"):
* Alexa, ask Becks when is my next bus to Clapham
* Alexa, ask Becks when will my next bus to Clapham will arrive
* Alexa, ask Becks when I should leave for Crystal Palace
* Alexa, ask Becks how long until the next bus for Croydon

This skill has a follow on to the first question, that being:
* Would you like me to remind you?

The skill is named: `myNextBus`.
The invocation name is: `Becks`.
The skill id: `amzn1.ask.skill.2ba78764-0a67-481f-907a-3f7c08287aeb`.

Other than the default 'built-in' intents, one new intent called `whenIs`. `whenIs` is to return a set of times, e.g. 10:05, 10:07 and 10:15. This has the utterances:
* `when is my next bus to {Destination}`.
* `when is my bus to {Destination} likely to arrive`
* `when should I leave for {Destination}`
* `when will my bus to {Destination} arrive`
* `when will my next bus to {Destination} arrive`
* `when will my {Destination} bus get here`
* `when is my next {Destination} bus`

When invoking the lambda endpoint, the event passed looks like:
```
{
    "event": {
        "version": "1.0",
        "session": {
            "new": true,
            "sessionId": "amzn1.echo-api.session.2f8f91d4-fed4-445f-bd2a-923c27db6072",
            "application": {
                "applicationId": "amzn1.ask.skill.2ba78764-0a67-481f-907a-3f7c08287aeb"
            },
            "user": {
                "userId": "amzn1.ask.account.AEGDLGI2QMONKDWOIBWRN7KLRXOYMNXOCN3MCECKPBE7SVWDSIEEVKVF7ZVHHPRBBMSETSBSB4BVV3RJVFEO4JT2TSJVTG7FJAGIS5RP2RTUFT5464HJRBTEI4C6BAMHLW6ZTNQ3QJHXBUKIE7ZX5YGOYBCEJCN57BDV7JAHWBH7ZR67C2TLUGQBW5TVVVVMHHUVYVAPADEAWWQ"
            }
        },
        "context": {
            "System": {
                "application": {
                    "applicationId": "amzn1.ask.skill.2ba78764-0a67-481f-907a-3f7c08287aeb"
                },
                "user": {
                    "userId": "amzn1.ask.account.AEGDLGI2QMONKDWOIBWRN7KLRXOYMNXOCN3MCECKPBE7SVWDSIEEVKVF7ZVHHPRBBMSETSBSB4BVV3RJVFEO4JT2TSJVTG7FJAGIS5RP2RTUFT5464HJRBTEI4C6BAMHLW6ZTNQ3QJHXBUKIE7ZX5YGOYBCEJCN57BDV7JAHWBH7ZR67C2TLUGQBW5TVVVVMHHUVYVAPADEAWWQ"
                },
                "device": {
                    "deviceId": "amzn1.ask.device.AE5J4BFMBNHEMPR6LEU467UGCDM526KOZYDQI4G6AT6DVK3ZMGYIZ47HWWJBMDTASR4AR3MU73RNOOZWIANDZ45AVAA6FWAFC57NJMU6L4JBNV6DDZJ7TIGBHBGFQS6ILEHMJKW53E25KMH4JS3NKQR4FKK5EAQ6HUW7L7AMR63CE23ZOAVRO",
                    "supportedInterfaces": {}
                },
                "apiEndpoint": "https://api.eu.amazonalexa.com",
                "apiAccessToken": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjEifQ.eyJhdWQiOiJodHRwczovL2FwaS5hbWF6b25hbGV4YS5jb20iLCJpc3MiOiJBbGV4YVNraWxsS2l0Iiwic3ViIjoiYW16bjEuYXNrLnNraWxsLjJiYTc4NzY0LTBhNjctNDgxZi05MDdhLTNmN2MwODI4N2FlYiIsImV4cCI6MTU0MzMzMzcwOSwiaWF0IjoxNTQzMzMwMTA5LCJuYmYiOjE1NDMzMzAxMDksInByaXZhdGVDbGFpbXMiOnsiY29uc2VudFRva2VuIjpudWxsLCJkZXZpY2VJZCI6ImFtem4xLmFzay5kZXZpY2UuQUU1SjRCRk1CTkhFTVBSNkxFVTQ2N1VHQ0RNNTI2S09aWURRSTRHNkFUNkRWSzNaTUdZSVo0N0hXV0pCTURUQVNSNEFSM01VNzNSTk9PWldJQU5EWjQ1QVZBQTZGV0FGQzU3TkpNVTZMNEpCTlY2RERaSjdUSUdCSEJHRlFTNklMRUhNSktXNTNFMjVLTUg0SlMzTktRUjRGS0s1RUFRNkhVVzdMN0FNUjYzQ0UyM1pPQVZSTyIsInVzZXJJZCI6ImFtem4xLmFzay5hY2NvdW50LkFFR0RMR0kyUU1PTktEV09JQldSTjdLTFJYT1lNTlhPQ04zTUNFQ0tQQkU3U1ZXRFNJRUVWS1ZGN1pWSEhQUkJCTVNFVFNCU0I0QlZWM1JKVkZFTzRKVDJUU0pWVEc3RkpBR0lTNVJQMlJUVUZUNTQ2NEhKUkJURUk0QzZCQU1ITFc2WlROUTNRSkhYQlVLSUU3Wlg1WUdPWUJDRUpDTjU3QkRWN0pBSFdCSDdaUjY3QzJUTFVHUUJXNVRWVlZWTUhIVVZZVkFQQURFQVdXUSJ9fQ.dJ9Kqwo_hRfw7kwqje9EpUUrHAy1ilPevpYIAYD_R_IuX-_WFLGu40xkRKs7B2vQAExwk0F3J7Eg6vb4QxEyuFku1yUR4G-NLPnGRIL9ZzjKhonHrTWCTyoOwMFQuHGWkPKLcIT42a-TePlf0HaUL3_1PNuHdig6ehdRCXplCy0qgIY7-AH510ywDJ3YbkbLhx-gVMnLFokrhdYZjcywIBE4qQgZULUSugKzZWDO0s3S_ii6bfxARmgrT-0oC_qXkcYd89zBKmL8X2d25L4aLtn6lC-EIIzp6g6962I92P1BtQ-bjH9jebceoXqTrR-XqQu4XEJbjxclPtxnA_LIsQ"
            },
            "Viewport": {
                "experiences": [
                    {
                        "arcMinuteWidth": 246,
                        "arcMinuteHeight": 144,
                        "canRotate": false,
                        "canResize": false
                    }
                ],
                "shape": "RECTANGLE",
                "pixelWidth": 1024,
                "pixelHeight": 600,
                "dpi": 160,
                "currentPixelWidth": 1024,
                "currentPixelHeight": 600,
                "touch": [
                    "SINGLE"
                ]
            }
        },
        "request": {
            "type": "IntentRequest",
            "requestId": "amzn1.echo-api.request.833e1635-eda6-4fb5-ae8b-f06e0453c135",
            "timestamp": "2018-11-27T14:48:29Z",
            "locale": "en-GB",
            "intent": {
                "name": "whenIs",
                "confirmationStatus": "NONE",
                "slots": {
                    "Destination": {
                        "name": "Destination",
                        "value": "Clapham",
                        "resolutions": {
                            "resolutionsPerAuthority": [
                                {
                                    "authority": "amzn1.er-authority.echo-sdk.amzn1.ask.skill.2ba78764-0a67-481f-907a-3f7c08287aeb.LIST_OF_DESTINATIONS",
                                    "status": {
                                        "code": "ER_SUCCESS_MATCH"
                                    },
                                    "values": [
                                        {
                                            "value": {
                                                "name": "Clapham",
                                                "id": "5a0251c093f8679f79d2fc9d474dd768"
                                            }
                                        }
                                    ]
                                }
                            ]
                        },
                        "confirmationStatus": "NONE",
                        "source": "USER"
                    }
                }
            },
            "dialogState": "STARTED"
        }
    }
}
```

Another intent called `howLong`. `howLong` is to return a set of durations, e.g. 3 minutes, 5 minutes and 10 minutes. This has the utterances:
* `how long until {Destination} bus`
* `how long until I can leave for {Destination}`
* `how long until my next bus to {Destination}`
* `how long must I wait for the {Destination} bus`
* `how much longer for the {Destination} bus`
* `how long must I wait before leaving to {Destination}`
* `how long till my next bus to {Destination}`

When invoking the lambda endpoint, the event passed looks like:
```
{
	"version": "1.0",
	"session": {
		"new": true,
		"sessionId": "amzn1.echo-api.session.2af9885a-7f55-4e56-96a4-79057d0dd0bf",
		"application": {
			"applicationId": "amzn1.ask.skill.2ba78764-0a67-481f-907a-3f7c08287aeb"
		},
		"user": {
			"userId": "amzn1.ask.account.AEGDLGI2QMONKDWOIBWRN7KLRXOYMNXOCN3MCECKPBE7SVWDSIEEVKVF7ZVHHPRBBMSETSBSB4BVV3RJVFEO4JT2TSJVTG7FJAGIS5RP2RTUFT5464HJRBTEI4C6BAMHLW6ZTNQ3QJHXBUKIE7ZX5YGOYBCEJCN57BDV7JAHWBH7ZR67C2TLUGQBW5TVVVVMHHUVYVAPADEAWWQ"
		}
	},
	"context": {
		"System": {
			"application": {
				"applicationId": "amzn1.ask.skill.2ba78764-0a67-481f-907a-3f7c08287aeb"
			},
			"user": {
				"userId": "amzn1.ask.account.AEGDLGI2QMONKDWOIBWRN7KLRXOYMNXOCN3MCECKPBE7SVWDSIEEVKVF7ZVHHPRBBMSETSBSB4BVV3RJVFEO4JT2TSJVTG7FJAGIS5RP2RTUFT5464HJRBTEI4C6BAMHLW6ZTNQ3QJHXBUKIE7ZX5YGOYBCEJCN57BDV7JAHWBH7ZR67C2TLUGQBW5TVVVVMHHUVYVAPADEAWWQ"
			},
			"device": {
				"deviceId": "amzn1.ask.device.AE5J4BFMBNHEMPR6LEU467UGCDM526KOZYDQI4G6AT6DVK3ZMGYIZ47HWWJBMDTASR4AR3MU73RNOOZWIANDZ45AVAA6FWAFC57NJMU6L4JBNV6DDZJ7TIGBHBGFQS6ILEHMJKW53E25KMH4JS3NKQR4FKK5EAQ6HUW7L7AMR63CE23ZOAVRO",
				"supportedInterfaces": {}
			},
			"apiEndpoint": "https://api.eu.amazonalexa.com",
			"apiAccessToken": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjEifQ.eyJhdWQiOiJodHRwczovL2FwaS5hbWF6b25hbGV4YS5jb20iLCJpc3MiOiJBbGV4YVNraWxsS2l0Iiwic3ViIjoiYW16bjEuYXNrLnNraWxsLjJiYTc4NzY0LTBhNjctNDgxZi05MDdhLTNmN2MwODI4N2FlYiIsImV4cCI6MTU0MzMzNjczNCwiaWF0IjoxNTQzMzMzMTM0LCJuYmYiOjE1NDMzMzMxMzQsInByaXZhdGVDbGFpbXMiOnsiY29uc2VudFRva2VuIjpudWxsLCJkZXZpY2VJZCI6ImFtem4xLmFzay5kZXZpY2UuQUU1SjRCRk1CTkhFTVBSNkxFVTQ2N1VHQ0RNNTI2S09aWURRSTRHNkFUNkRWSzNaTUdZSVo0N0hXV0pCTURUQVNSNEFSM01VNzNSTk9PWldJQU5EWjQ1QVZBQTZGV0FGQzU3TkpNVTZMNEpCTlY2RERaSjdUSUdCSEJHRlFTNklMRUhNSktXNTNFMjVLTUg0SlMzTktRUjRGS0s1RUFRNkhVVzdMN0FNUjYzQ0UyM1pPQVZSTyIsInVzZXJJZCI6ImFtem4xLmFzay5hY2NvdW50LkFFR0RMR0kyUU1PTktEV09JQldSTjdLTFJYT1lNTlhPQ04zTUNFQ0tQQkU3U1ZXRFNJRUVWS1ZGN1pWSEhQUkJCTVNFVFNCU0I0QlZWM1JKVkZFTzRKVDJUU0pWVEc3RkpBR0lTNVJQMlJUVUZUNTQ2NEhKUkJURUk0QzZCQU1ITFc2WlROUTNRSkhYQlVLSUU3Wlg1WUdPWUJDRUpDTjU3QkRWN0pBSFdCSDdaUjY3QzJUTFVHUUJXNVRWVlZWTUhIVVZZVkFQQURFQVdXUSJ9fQ.fUrdX09dXIG15kQaXXVW_EPkSWVvdTiEgFRerttdsi5W3o3tcYaAAME6_BQ5yfGP1GDvgqgq_TcugZVhceTNVJJnirYbB21A1AdGg2FLYtUh67ndsqXhr5dlbwPugGDZ7k5FOhfQr516a7u6CZezL3kTAjiI4XbHauttkkcalrPCpeFEWBBww7HaXYXChI4smSze4Ktf8JHbMPMyMOeFsFfbPK5AEboyMJCfWjCMJmuz66m9h0-sOL9E8cxet2l-yHM1osv_2zJ7GkcAl5cJiBF85nCl2j7fIBD5legGjRpxuMaV9vtHVtfiMU41khcXMgxnyquqjqTTsqr2JCNZOQ"
		},
		"Viewport": {
			"experiences": [
				{
					"arcMinuteWidth": 246,
					"arcMinuteHeight": 144,
					"canRotate": false,
					"canResize": false
				}
			],
			"shape": "RECTANGLE",
			"pixelWidth": 1024,
			"pixelHeight": 600,
			"dpi": 160,
			"currentPixelWidth": 1024,
			"currentPixelHeight": 600,
			"touch": [
				"SINGLE"
			]
		}
	},
	"request": {
		"type": "IntentRequest",
		"requestId": "amzn1.echo-api.request.3d2fe165-8a8f-425d-b286-5dc4a0fa5e4d",
		"timestamp": "2018-11-27T15:38:54Z",
		"locale": "en-GB",
		"intent": {
			"name": "howLong",
			"confirmationStatus": "NONE",
			"slots": {
				"Destination": {
					"name": "Destination",
					"value": "Clapham",
					"resolutions": {
						"resolutionsPerAuthority": [
							{
								"authority": "amzn1.er-authority.echo-sdk.amzn1.ask.skill.2ba78764-0a67-481f-907a-3f7c08287aeb.LIST_OF_DESTINATIONS",
								"status": {
									"code": "ER_SUCCESS_MATCH"
								},
								"values": [
									{
										"value": {
											"name": "Clapham",
											"id": "5a0251c093f8679f79d2fc9d474dd768"
										}
									}
								]
							}
						]
					},
					"confirmationStatus": "NONE",
					"source": "USER"
				}
			}
		},
		"dialogState": "STARTED"
	}
}
```

`{Destination}` is the slot; this is a list of known destinations (known to me):
* Brixton
* Clapham
* Croydon
* Crystal Palace
* ...

The `{Destination}` slot is marked as required within `Slot Filling`. If not given, Alexa will prompt (dialog); this applied to both `whenIs` and `howLong` intents. For example:
* I ask: Alexa, when is my next bus?
	* Akexa responds with: To which destination?
		* I say: Clapham.

Be sure to add a few responses for missing slots; Alexa Skill will choose at random from the list, giving a more natural response.

The `Destination` slot also has valiation, enforcing that it must be one of the listed slot values.

> Note, I was expecting Alexa Skill to hold it's own conversation if the `{Destination}` was not populated. Oh so no, the request is sent to your handler (lambda in my case), you must determine that the `{Destination}` is missing and return a `Dialog.Delegate`: https://developer.amazon.com/docs/custom-skills/define-the-dialog-to-collect-and-confirm-required-information.html#intent-slots-order.

> When using the Alxea Developer Console Build, be sure to select the "+" buttons when defining dialog content; nothing happens until you **add**.

> When using the Alexa Developer Console Test Simulator, when the intent is fired and a request is sent to lambda, the simulator will show the JSON sent in the lambda `event` object

The Alexa Skill JSON:
```
{
    "interactionModel": {
        "languageModel": {
            "invocationName": "becks",
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
                    "name": "whenIs",
                    "slots": [
                        {
                            "name": "Destination",
                            "type": "LIST_OF_DESTINATIONS",
                            "samples": [
                                "to {Destination}",
                                "{Destination}"
                            ]
                        }
                    ],
                    "samples": [
                        "when is my next {Destination} bus",
                        "when will my {Destination} bus get here",
                        "when is my bus to {Destination} likely to arrive",
                        "when should I leave for {Destination}",
                        "when will my bus to {Destination} arrive",
                        "when will my next bus to {Destination} arrive",
                        "when is my next bus to {Destination}"
                    ]
                },
                {
                    "name": "howLong",
                    "slots": [
                        {
                            "name": "Destination",
                            "type": "LIST_OF_DESTINATIONS",
                            "samples": [
                                "to {Destination}",
                                "{Destination}"
                            ]
                        }
                    ],
                    "samples": [
                        "how long until {Destination} bus",
                        "how long until I can leave for {Destination}",
                        "how long until my next bus to {Destination}",
                        "how long must I wait for the {Destination} bus",
                        "how much longer for the {Destination} bus",
                        "how long must I wait before leaving to {Destination}",
                        "how long till my next bus to {Destination}"
                    ]
                }
            ],
            "types": [
                {
                    "name": "LIST_OF_DESTINATIONS",
                    "values": [
                        {
                            "name": {
                                "value": "Crystal Palace"
                            }
                        },
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
                    "name": "whenIs",
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
                            },
                            "validations": [
                                {
                                    "type": "hasEntityResolutionMatch",
                                    "prompt": "Slot.Validation.1445897530190.1427925612691.1531545622963"
                                }
                            ]
                        }
                    ]
                },
                {
                    "name": "howLong",
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
                            },
                            "validations": [
                                {
                                    "type": "hasEntityResolutionMatch",
                                    "prompt": "Slot.Validation.885844913883.869449845389.472556168862"
                                }
                            ]
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
                        "value": "Sorry, but where are you going"
                    },
                    {
                        "type": "PlainText",
                        "value": "Where are you going to"
                    },
                    {
                        "type": "PlainText",
                        "value": "To which destination?"
                    }
                ]
            },
            {
                "id": "Slot.Validation.1445897530190.1427925612691.1531545622963",
                "variations": [
                    {
                        "type": "PlainText",
                        "value": "I only know of the following destinations, {Destination}"
                    }
                ]
            },
            {
                "id": "Slot.Validation.885844913883.869449845389.472556168862",
                "variations": [
                    {
                        "type": "PlainText",
                        "value": "Sorry, I only know of the following destinations, {Destination}"
                    }
                ]
            }
        ]
    }
}
```

# Lambda Function
All code can be found here: https://github.com/wozitech/alexa.

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
            "Resource": "arn:aws:secretsmanager:${var.region}:${var.account}:secret:TFL_API_Portal-abc123"
        }
```

The `...tfl_lambda_role` role is attributed to "eu-west-1", rather than "eu-west_2" because Alexa Skill triggers not available in "eu-west-2". For efficiency, lambda/Secrets should be the same zone; namely, eu-west-1; this is enforced within the code by instantiating `AWS.SecretsManager` using the region extracted from lambda context:
* In https://github.com/wozitech/alexa/blob/master/src/api/myBuses.js:
```
  var arnList = (context.invokedFunctionArn).split(":");
  var lambdaRegion = arnList[3];
```
	
In https://github.com/wozitech/alexa/blob/master/src/aws/secrets.js:
```
    var secrets = new AWS.SecretsManager({
        region: lambdaRegion
    });
```

# TODO
* A customised source; currently assumes 'my house'. Should locate the "tfl stop points" nearest to the location of the given Alexa when the skill is added. Allow for the 'Alexa location' to be overriden.
* A customised set of destinations; currently assumes 'my given destinations' only. For each source, show the lines that run through that source, allowing a 'destination' to be assigned.
* When formatting the response, for the assumed source of the given destination, need to allow for the walking time to the bus stop.