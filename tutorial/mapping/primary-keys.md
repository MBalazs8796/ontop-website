# Role of primary keys (unique constraints)


Unique constraints (such as primary keys) are very useful for removing self-joins and thus improving query answering performance.

Let us now consider the following files: [university-no-pk.ttl](university-no-pk.ttl), [university-no-pk.obda](university-no-pk.obda) and [university-no-pk.properties](university-no-pk.properties) files.
The mapping assertions are the same as during the first session.
The only difference is that primary keys have been removed.

Open the new ontology file in Protégé and run the following SPARQL query:

```sparql
PREFIX : <http://example.org/voc#>
PREFIX foaf: <http://xmlns.com/foaf/0.1/>

SELECT DISTINCT ?prof ?lastName {
  ?prof a :FullProfessor ;
        foaf:firstName ?firstName ;
        foaf:lastName ?lastName .
}
```

Due to the absence of primary keys, the generated SQL is, after some minor reformatting, the following:

```sql
SELECT *
FROM (

    SELECT 1 AS "profQuestType", NULL AS "profLang",
           ('http://example.org/uni2/person/' || QVIEW1."pid") AS "prof",
           7 AS "lastNameQuestType", NULL AS "lastNameLang",
           QVIEW3."lname" AS "lastName"
    FROM   "uni2"."person" QVIEW1,
           "uni2"."person" QVIEW2,
           "uni2"."person" QVIEW3
    WHERE  (QVIEW1."status" = 7) AND
           QVIEW1."pid" IS NOT NULL AND
           (QVIEW1."pid" = QVIEW2."pid") AND
           QVIEW2."fname" IS NOT NULL AND
           (QVIEW1."pid" = QVIEW3."pid") AND
           QVIEW3."lname" IS NOT NULL

    UNION

    SELECT 1 AS "profQuestType", NULL AS "profLang",
           ('http://example.org/uni1/academic/' || QVIEW1."a_id") AS "prof",
           7 AS "lastNameQuestType", NULL AS "lastNameLang",
           QVIEW3."last_name" AS "lastName"
    FROM   "uni1"."academic" QVIEW1,
           "uni1"."academic" QVIEW2,
           "uni1"."academic" QVIEW3
    WHERE  (QVIEW1."position" = 1) AND
           QVIEW1."a_id" IS NOT NULL AND
           (QVIEW1."a_id" = QVIEW2."a_id") AND
           QVIEW2."first_name" IS NOT NULL AND
           (QVIEW1."a_id" = QVIEW3."a_id") AND
           QVIEW3."last_name" IS NOT NULL

) SUB_QVIEW
```

In each sub-query, one can observe two self-joins, between `QVIEW1`, `QVIEW2` and `QVIEW3`.

If you run the same query with the setting of the first session, you will obtain the following query:

```sql
SELECT 1 AS "pQuestType", NULL AS "pLang",
       ('http://example.org/uni2/person/' || QVIEW1."pid" ) AS "p",
       7 AS "lastNameQuestType", NULL AS "lastNameLang",
       QVIEW1."lname" AS "lastName"
FROM   "uni2"."person" QVIEW1
WHERE  QVIEW1."pid" IS NOT NULL AND
       QVIEW1."fname" IS NOT NULL AND
       QVIEW1."lname" IS NOT NULL

UNION ALL

SELECT 1 AS "pQuestType", NULL AS "pLang",
       ('http://example.org/uni1/academic/' || QVIEW1."a_id" ) AS "p",
       7 AS "firstNameQuestType", NULL AS "firstNameLang",
       QVIEW1."first_name" AS "firstName",
       7 AS "lastNameQuestType", NULL AS "lastNameLang",
       QVIEW1."last_name" AS "lastName"
FROM   "uni1"."academic" QVIEW1
WHERE  QVIEW1."a_id" IS NOT NULL AND
       QVIEW1."first_name" IS NOT NULL AND
       QVIEW1."last_name" IS NOT NULL

UNION ALL

SELECT 1 AS "pQuestType", NULL AS "pLang",
       ('http://example.org/uni1/student/' || QVIEW1."s_id" ) AS "p",
       7 AS "firstNameQuestType", NULL AS "firstNameLang",
       QVIEW1."first_name" AS "firstName",
       7 AS "lastNameQuestType", NULL AS "lastNameLang",
       QVIEW1."last_name" AS "lastName"
FROM   "uni1"."student" QVIEW1
WHERE  QVIEW1."s_id" IS NOT NULL AND
       QVIEW1."first_name" IS NOT NULL AND
       QVIEW1."last_name" IS NOT NULL
```

As you can see, the self-joins are removed when primary keys are provided and used as joining conditions.

