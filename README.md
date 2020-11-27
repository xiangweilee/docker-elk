# Docker ELK

ELK stack in Docker container

Filebeat + Logstash + Elasticsearch + Kibana in one docker-composer.yml file

> Here we use apache.log file a an example

# Create the index

<details>
  <summary>Code</summary>

```shell
$ curl --request POST 'https://vpc-application-index-es-stage-hqo6aa6sarqhaxnhg6tgmp6bre.ap-southeast-1.es.amazonaws.com/staging_my_resume'
{"acknowledged":true,"shards_acknowledged":true,"index":"staging_my_resume"}
```

</details>

# Close the index

<details>
  <summary>Code</summary>

```shell
$ curl --request POST 'https://vpc-application-index-es-stage-hqo6aa6sarqhaxnhg6tgmp6bre.ap-southeast-1.es.amazonaws.com/staging_my_resume/_close'
{"acknowledged":true,"shards_acknowledged":true,"indices":{"staging_my_resume":{"closed":true}}}
```

</details>

# Update the index settings

<details>
  <summary>Code</summary>

```shell
$ curl --request PUT 'https://vpc-application-index-es-stage-hqo6aa6sarqhaxnhg6tgmp6bre.ap-southeast-1.es.amazonaws.com/staging_my_resume/_settings' \
--header 'Content-Type: application/json' \
--data-raw '{
    "analysis": {
      "filter": {
        "positionSynFilter": {
          "type": "synonym",
          "synonyms_path": "analyzers/F144516798"
        },
        "skillSynFilter": {
          "type": "synonym",
          "synonyms_path": "analyzers/F212720903"
        }
      },
      "analyzer": {
        "text_skill": {
          "filter": [
            "lowercase",
            "skillSynFilter",
            "unique",
            "asciifolding"
          ],
          "index": "analyzed",
          "type": "custom",
          "tokenizer": "whiteSpaceTokenizer"
        },
        "text_general": {
          "filter": [
            "lowercase",
            "asciifolding",
            "unique",
            "asciifolding"
          ],
          "index": "analyzed",
          "type": "custom",
          "tokenizer": "standardTokenizer"
        },
        "text_position_title": {
          "filter": [
            "lowercase",
            "positionSynFilter",
            "unique",
            "asciifolding"
          ],
          "index": "analyzed",
          "type": "custom",
          "tokenizer": "whiteSpaceTokenizer"
        }
      },
      "tokenizer": {
        "standardTokenizer": {
          "type": "standard"
        },
        "whiteSpaceTokenizer": {
          "type": "whitespace"
        }
      }
    }
  }'
{"acknowledged": true}
```

</details>

# Open the index

<details>
  <summary>Code</summary>

```shell
$ curl --request POST 'https://vpc-application-index-es-stage-hqo6aa6sarqhaxnhg6tgmp6bre.ap-southeast-1.es.amazonaws.com/staging_my_resume/_open'
{"acknowledged":true,"shards_acknowledged":true}
```

</details>

# Update the index mapping

<details>
  <summary>Code</summary>

```shell
$ curl --location --request PUT 'https://vpc-application-index-es-stage-hqo6aa6sarqhaxnhg6tgmp6bre.ap-southeast-1.es.amazonaws.com/staging_my_resume/_mapping' \
--header 'Content-Type: application/json' \
--data-raw '{
  "properties": {
    "candidate": {
      "properties": {
        "candidate_misc_code": {
          "type": "integer"
        },
        "start_work_date": {
          "type": "date"
        },
        "uuid": {
          "type": "text"
        }
      }
    },
    "education": {
      "type": "nested",
      "properties": {
        "college": {
          "properties": {
            "name": {
              "type": "text",
              "analyzer": "text_general"
            }
          }
        },
        "field_of_study": {
          "properties": {
            "code": {
              "type": "integer"
            },
            "name": {
              "type": "text",
              "analyzer": "text_general"
            },
            "other": {
              "type": "text",
              "analyzer": "text_general"
            }
          }
        },
        "graduation_date": {
          "type": "date"
        },
        "qualification": {
          "properties": {
            "code": {
              "type": "integer"
            },
            "name": {
              "type": "text",
              "analyzer": "text_general"
            },
            "siva_code": {
              "type": "integer"
            }
          }
        }
      }
    },
    "highest_education": {
      "type": "nested",
      "properties": {
        "additional_info": {
          "type": "text",
          "analyzer": "text_general"
        },
        "average_grade": {
          "properties": {
            "code": {
              "type": "integer"
            },
            "name": {
              "type": "text",
              "analyzer": "text_general"
            },
            "siva_code": {
              "type": "integer"
            }
          }
        },
        "cgpa": {
          "properties": {
            "base": {
              "type": "float"
            },
            "score": {
              "type": "float"
            }
          }
        },
        "college": {
          "properties": {
            "country": {
              "properties": {
                "code": {
                  "type": "integer"
                },
                "iso_code": {
                  "type": "text"
                },
                "name": {
                  "type": "text",
                  "analyzer": "text_general"
                }
              }
            },
            "name": {
              "type": "text",
              "analyzer": "text_general"
            }
          }
        },
        "field_of_study": {
          "properties": {
            "code": {
              "type": "integer"
            },
            "name": {
              "type": "text",
              "analyzer": "text_general"
            },
            "other": {
              "type": "text",
              "analyzer": "text_general"
            }
          }
        },
        "graduation_date": {
          "type": "date"
        },
        "major": {
          "type": "text",
          "analyzer": "text_general"
        },
        "qualification": {
          "properties": {
            "code": {
              "type": "integer"
            },
            "name": {
              "type": "text",
              "analyzer": "text_general"
            },
            "siva_code": {
              "type": "integer"
            }
          }
        }
      }
    },
    "language": {
      "type": "nested",
      "properties": {
        "code": {
          "type": "integer"
        },
        "name": {
          "type": "text"
        },
        "spoken_score": {
          "type": "integer"
        },
        "written_score": {
          "type": "integer"
        }
      }
    },
    "matching": {
      "properties": {
        "c1_industry_siva_code": {
          "type": "integer"
        },
        "c1_position_level_siva_code": {
          "type": "integer"
        },
        "c1_position_title": {
          "type": "text",
          "analyzer": "text_general",
          "position_increment_gap": 100
        },
        "c1_position_title_syn": {
          "type": "text",
          "analyzer": "text_position_title",
          "position_increment_gap": 100
        },
        "c1_specialization_code": {
          "type": "integer"
        },
        "c2_industry_siva_code": {
          "type": "integer"
        },
        "c2_position_level_siva_code": {
          "type": "integer"
        },
        "c2_position_title": {
          "type": "text",
          "analyzer": "text_general",
          "position_increment_gap": 100
        },
        "c2_position_title_syn": {
          "type": "text",
          "analyzer": "text_position_title",
          "position_increment_gap": 100
        },
        "c2_specialization_code": {
          "type": "integer"
        },
        "highest_qualification_siva_code": {
          "type": "integer"
        }
      }
    },
    "myjs_resume_id": {
      "type": "integer"
    },
    "myjs_server_id": {
      "type": "integer"
    },
    "personal": {
      "properties": {
        "address": {
          "properties": {
            "city": {
              "type": "text",
              "analyzer": "text_general"
            },
            "postcode": {
              "type": "keyword"
            }
          }
        },
        "email": {
          "type": "text"
        },
        "name": {
          "properties": {
            "first": {
              "type": "text",
              "analyzer": "text_general"
            },
            "last": {
              "type": "text",
              "analyzer": "text_general"
            }
          }
        },
        "nationality": {
          "properties": {
            "code": {
              "type": "integer"
            },
            "iso_code": {
              "type": "keyword"
            },
            "name": {
              "type": "text",
              "analyzer": "text_general"
            }
          }
        }
      }
    },
    "preference": {
      "properties": {
        "expected_salary": {
          "type": "float"
        },
        "expected_salary_currency": {
          "type": "keyword"
        },
        "preferred_work_locations": {
          "type": "nested",
          "include_in_parent": true,
          "properties": {
            "code": {
              "type": "integer"
            },
            "name": {
              "type": "text",
              "analyzer": "text_general"
            }
          }
        }
      }
    },
    "primary_language": {
      "properties": {
        "code": {
          "type": "integer"
        },
        "name": {
          "type": "text"
        }
      }
    },
    "resume": {
      "properties": {
        "direct_application_flag": {
          "type": "boolean"
        },
        "priority_application_flag": {
          "type": "boolean"
        }
      }
    },
    "siva_resume_id": {
      "type": "integer"
    },
    "skill": {
      "type": "nested",
      "properties": {
        "code": {
          "type": "integer"
        },
        "description": {
          "type": "text",
          "analyzer": "text_general"
        },
        "description_syn": {
          "type": "text",
          "analyzer": "text_skill"
        },
        "name": {
          "type": "text"
        }
      }
    },
    "work_experience": {
      "type": "nested",
      "properties": {
        "company": {
          "properties": {
            "country": {
              "type": "object"
            },
            "name": {
              "type": "text",
              "analyzer": "text_general"
            }
          }
        },
        "industry": {
          "properties": {
            "code": {
              "type": "integer"
            },
            "name": {
              "type": "text"
            }
          }
        },
        "joined_date": {
          "type": "date"
        },
        "left_date": {
          "type": "date"
        },
        "position_level": {
          "properties": {
            "code": {
              "type": "integer"
            },
            "name": {
              "type": "text",
              "analyzer": "text_general"
            },
            "siva_code": {
              "type": "integer"
            }
          }
        },
        "position_title": {
          "type": "text",
          "analyzer": "text_general"
        },
        "position_title_syn": {
          "type": "text",
          "analyzer": "text_position_title"
        },
        "specialization": {
          "properties": {
            "code": {
              "type": "integer"
            },
            "name": {
              "type": "text"
            }
          }
        },
        "work_description": {
          "type": "text",
          "analyzer": "text_general"
        }
      }
    },
    "applications": {
      "type": "nested",
      "properties": {
        "advertiser_id": {
          "type": "integer"
        },
        "application_id": {
          "type": "integer"
        },
        "application_pitch": {
          "type": "text",
          "analyzer": "text_general"
        },
        "application_source": {
          "properties": {
            "code": {
              "type": "integer"
            },
            "name": {
              "type": "text"
            },
            "siva_code": {
              "type": "integer"
            }
          }
        },
        "application_status": {
          "properties": {
            "code": {
              "type": "integer"
            }
          }
        },
        "apply_date": {
          "type": "date"
        },
        "candidate_uuid": {
          "type": "keyword"
        },
        "job_id": {
          "type": "integer"
        },
        "last_active_date": {
          "type": "date"
        },
        "last_update": {
          "properties": {
            "date": {
              "type": "date"
            },
            "svuser_id": {
              "type": "text"
            }
          }
        },
        "last_updated": {
          "properties": {
            "date": {
              "type": "date"
            },
            "svuser_id": {
              "type": "integer"
            }
          }
        },
        "priority_application_flag": {
          "type": "boolean"
        },
        "rolerequirement": {
          "properties": {
            "questionnaire_url": {
              "type": "text"
            },
            "score": {
              "type": "long"
            },
            "sequence": {
              "type": "long"
            },
            "total_questions": {
              "type": "long"
            }
          }
        },
        "withdraw_date": {
          "type": "date"
        }
      }
    }
  }
}'
{"acknowledged":true,"shards_acknowledged":true}
```

</details>

# Create a nested document

<details>
  <summary>Code</summary>

```shell
$ curl --location --request PUT 'https://vpc-application-index-es-stage-hqo6aa6sarqhaxnhg6tgmp6bre.ap-southeast-1.es.amazonaws.com/_doc/51894735' \
--header 'Content-Type: application/json' \
--data-raw '{
  "siva_resume_id": 51894735,
  "myjs_resume_id": 0,
  "myjs_server_id": 0,
  "candidate": {
    "uuid": "B634B1AC-18CD-11E8-AAD2-00505680053B",
    "candidate_misc_code": 0,
    "start_work_date": "2018-08-08T16:00:00Z"
  },
  "resume": {
    "priority_application_flag": false,
    "direct_application_flag": false
  },
  "personal": {
    "name": {
      "first": "Alvin",
      "last": "Tan"
    },
    "email": "alvintan.2014@example.com",
    "nationality": {
      "code": "190",
      "name": "Singapore",
      "iso_code": "SG"
    },
    "address": {
      "city": "",
      "postcode": ""
    }
  },
  "preference": {
    "expected_salary": 3800,
    "expected_salary_currency": "SGD",
    "preferred_work_locations": [
      {
        "code": 70000,
        "name": "Across Singapore"
      },
      {
        "code": 100000,
        "name": "Across China"
      },
      {
        "code": 150000,
        "name": "Across Japan"
      }
    ]
  },
  "education": [
    {
      "qualification": {
        "code": 4,
        "name": "Bachelor's Degree",
        "siva_code": 64
      },
      "field_of_study": {
        "code": 8,
        "name": "Computer Science\/Information Technology",
        "other": ""
      },
      "college": {
        "name": "Singapore Management University"
      },
      "graduation_date": "2018-06-01T00:00:00Z"
    }
  ],
  "matching": {
    "highest_qualification_siva_code": 64,
    "c1_position_title": "UX & UI Designer",
    "c1_position_title_syn": "UX & UI Designer",
    "c1_industry_siva_code": 10,
    "c1_position_level_siva_code": 64,
    "c1_specialization_code": 191,
    "c2_position_title": "UX & UI Design Intern",
    "c2_position_title_syn": "UX & UI Design Intern",
    "c2_industry_siva_code": 3,
    "c2_position_level_siva_code": 64,
    "c2_specialization_code": 191
  },
  "work_experience": [
    {
      "position_title": "UX & UI Designer",
      "joined_date": "2017-05-03T00:00:00Z",
      "left_date": "2017-08-03T00:00:00Z",
      "work_description": "- Worked on the \"Business Grant Portal\" as a UX & UI Designer in a Scrum Agile Framework\r\n- Created extension and interactive prototypes for user testing\r\n- Conducted Usability Testingon prototypes to gather feedback\r\n- Resolved user pain points\/problems through observations during user testing\r\n- Designed UX and UI based on observation, research and user's feedback\r\n- Designed illustrations and icons with the users, product and branding in mind\r\n- Implemented some tasks\/stories in React while working as a Designer for the project",
      "company": {
        "name": "GovTech Singapore"
      },
      "position_level": {
        "code": 5,
        "name": "Entry Level",
        "siva_code": 64
      },
      "industry": {
        "code": 10,
        "name": "Computer\/Information Technology (Software)"
      },
      "specialization": {
        "code": 191,
        "name": "IT\/Computer - Software"
      }
    },
    {
      "position_title": "UX & UI Design Intern",
      "joined_date": "2016-05-03T00:00:00Z",
      "left_date": "2016-08-03T00:00:00Z",
      "work_description": "- Conceptualized and created storyboards for a Jergen's web\/social media video advertisement\r\n- Created wireframes for a client\u2019s website to observe and gather feedback on the UX & UI\r\n- Designed graphic collaterals, ad banners and icons for clients (Visa, IKEA, ThyssenKrupp)\r\n- Animated graphic illustrations on the web using JavaScript\r\n- Designed responsive websites on CMS like Tumblr\r\n- Designed UI components for websites",
      "company": {
        "name": "Papier Blanc"
      },
      "position_level": {
        "code": 5,
        "name": "Entry Level",
        "siva_code": 64
      },
      "industry": {
        "code": 3,
        "name": "Advertising\/Marketing\/Promotion\/PR"
      },
      "specialization": {
        "code": 191,
        "name": "IT\/Computer - Software"
      }
    }
  ],
  "skill": [
    {
      "code": null,
      "name": "HTML"
    },
    {
      "code": null,
      "name": "CSS"
    },
    {
      "code": null,
      "name": "Javascript"
    },
    {
      "code": null,
      "name": "Adobe After Effects"
    },
    {
      "code": null,
      "name": "Adobe Experience Design"
    },
    {
      "code": null,
      "name": "Axure"
    },
    {
      "code": null,
      "name": "InVision"
    },
    {
      "code": null,
      "name": "Sketch"
    },
    {
      "code": null,
      "name": "Adobe Illustrator"
    },
    {
      "code": null,
      "name": "Adobe Photoshop"
    }
  ],
  "language": [
    {
      "code": 10,
      "name": "Korean",
      "spoken_score": 4,
      "written_score": 4
    },
    {
      "code": 2,
      "name": "Chinese",
      "spoken_score": 10,
      "written_score": 10
    },
    {
      "code": 3,
      "name": "English",
      "spoken_score": 10,
      "written_score": 10
    }
  ],
  "primary_language": {
    "code": 3,
    "name": "English"
  },
  "applications": [
    {
      "application_source": {
        "code": 25,
        "name": "JobStreet.com (Mobile Apply)",
        "siva_code": 2048
      },
      "advertiser_id": 32678,
      "job_id": 7593885,
      "priority_application_flag": false,
      "candidate_uuid": "13D1F49C-A18C-11E6-A79B-005056A279C0",
      "application_id": 291971721,
      "application_pitch": "",
      "application_status": {
        "code": 100000
      },
      "last_updated": {
        "date": "2019-10-23T16:04:32Z",
        "svuser_id": ""
      },
      "withdraw_date": null,
      "apply_date": "2019-10-23T16:04:32Z",
      "last_active_date": null,
      "rolerequirement": {
        "questionnaire_url": "",
        "score": -1,
        "sequence": 0,
        "total_questions": 0
      }
    },
    {
      "application_source": {
        "code": 25,
        "name": "JobStreet.com (Mobile Apply)",
        "siva_code": 2048
      },
      "advertiser_id": 32678,
      "job_id": 6593885,
      "priority_application_flag": false,
      "candidate_uuid": "13D1F49C-A18C-11E6-A79B-005056A279C0",
      "application_id": 291971721,
      "application_pitch": "",
      "application_status": {
        "code": 100000
      },
      "last_updated": {
        "date": "2019-10-23T16:04:32Z",
        "svuser_id": ""
      },
      "withdraw_date": null,
      "apply_date": "2019-10-23T16:04:32Z",
      "last_active_date": null,
      "rolerequirement": {
        "questionnaire_url": "",
        "score": -1,
        "sequence": 0,
        "total_questions": 0
      }
    },
  ]
}'
{"_index":"staging_my_resume","_type":"_doc","_id":"51894735","_version":1,"result":"created","_shards":{"total":2,"successful":1,"failed":0},"_seq_no":0,"_primary_term":3}
```

</details>
