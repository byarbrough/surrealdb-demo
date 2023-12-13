# PAX-DB

A personnel database using SurrealDB.

## Usage

```bash
podman run --rm -it -p 8000:8000 -v `pwd`/mydata:/mydata docker.io/surrealdb/surrealdb:latest start --auth --user root --pass root
```

I plan on using a Jupyter notebook to run the creation of the databse because the Go SDK is pretty basic.
Ok, it's stupid, honestly, and doesn't use any good Go features.

### Relate

```
# DEFINE TABLE member SCHEMAFULL;
# DEFINE FIELD firstName ON TABLE member TYPE string;
# DEFINE FIELD lastName ON TABLE member TYPE string;
# DEFINE FIELD dodId ON TABLE member TYPE string
#     ASSERT string::is::numeric(dodId) && string::len(dodId) == 10;
# DEFINE INDEX dodIdIndex ON TABLE member COLUMNS dodId UNIQUE;
# 
# select * from member;
# create member content {firstName: "bob", lastName: "smith", dodId: "0123456789"};
# select * from member2;
select * from member;
select * from member2;
select * from punched;
# let $curMember = (select id from member where dodId = "0123456789");
# 
# relate $curMember->rated->member:88a253nbrqdd60durmun
#     set rating = 5;
# select * from rated;
# define table punched schemafull;
# define field fingers on table punched type int;
# 

# relate member:->punched->member2:sxgbjq16jv48qo6fqjs2 set fingers = 5;

# who has been punched by
# select out.firstName from punched where in = member:bplxz8dl5e05y6fj6s7o;
# delete member:bplxz8dl5e05y6fj6s7o;
```

## feedback

Way one is with relate https://docs.surrealdb.com/docs/surrealql/statements/relate/

```
# create feedback content {'content': "terrible employee", "body": "fine", "date": time::now()};
select * from gaveFeedback;
select * from feedback;
# relate member:0n8ude84gxl4imdtkhnq->gaveFeedback->member:147f4liwpa7cv7095t5a set feedbackId = feedback:ukkckqyxlpx3ot8zcxrx;
# select ->gaveFeedback.feedbackId.content from member:0n8ude84gxl4imdtkhnq
```

Way two is with records https://docs.surrealdb.com/docs/surrealql/datamodel/records/
This one makes for more sane queries in the common case (give me feedback on the employee)
```
# DEFINE FIELD employeeFeedback ON TABLE member TYPE array;
# DEFINE FIELD employeeFeedback.* ON TABLE member TYPE record<feedback>;
# INFO FOR TABLE member;
# UPDATE ONLY member:147f4liwpa7cv7095t5a SET employeeFeedback = [feedback:ljpmkivfh4vem6bspitv];
SELECT employeeFeedback.content FROM member:147f4liwpa7cv7095t5a WHERE employeeFeedback.date > 2023-12-11; 
# WHERE employeeId = '13786';
```