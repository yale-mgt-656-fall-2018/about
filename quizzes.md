# Taking quizzes

Most of the class meetings include pre-class reading and a
quiz that covers that reading. You will take those quizzes
using the [class API](https://www.cpsc213.io/openapi/).
(We are working on adding quiz-taking to the class website
and we'll have it done soon. In the meantime, we apologize
for your suffering and hope that, at the minimum, it is 
character building.)

To use the API, there are a few steps:

1. Get your [JSON Web Token](https://jwt.io/introduction/) for the class API. This identifies you
   and allows you interact with the API (though, there are some things you can do anonymously).
2. Find the next class session.
3. Find the quiz corresponding to that class.
4. Create a quiz submission.
5. Show the quiz questions and choices.
6. Submit your choice for each question.

You can do this using any tools you like. For example, it is easy to write a shell, JavaScript, Ruby, or Python script for interacting with the class API. You can also use programs like Postman, which give you a GUI for interacting with APIs. Here, I am going to show you to how do this all "by hand" on the command line using CURL, a common command line HTTP client you will find available on most operating systems. Using CURL, your CLI session will end up looking like [this](https://asciinema.org/a/0RwxP8TNQWHT9Bt6ZTPUM36gV).

 ## Get your JWT

 You should log into the [class website](https://www.cpsc213.io/) and then visit
 your [dashboard](https://www.cpsc213.io/#/dashboard), which shows a few pieces of
 information, including a freshly generated JWT. (The JWT will expire in two days
 or so.) You want to copy this value. If you're on a
 [Posix compliant](https://stackoverflow.com/questions/1780599/i-never-really-understood-what-is-posix)
 shell, like Bash, which is likely, you can do this as follows:

 ```
 export JWT="eyJhbGciOiJIUzI1NiIsInR5c..."
 ```

 Above, the "..." indicates that I elided the rest of that value. 


 2. Find the next class session.

 ```
 curl 'https://www.cpsc213.io/rest/meetings'
 ```

 That will dump a lot of information, and you don't need it all. You're
 probably only interested in the *next* few classes. So, add a query as 
 follows


 ```
 curl 'https://www.cpsc213.io/rest/meetings&begins_at=gt.2018-01-17&limit=3'
 ```

 This says, "get the first three classes with `begins_at` greater than 2018-01-17".
 Now, there is still too  much information, so let's get only the `meeting_id`,
 `begins_at`, `title`, and `slug`. (The "slug" is the URL-friendly identifier.)
 The query format is described in the 
 PostgREST documentation](https://postgrest.com/en/latest/api.html).

 ```
 curl 'https://www.cpsc213.io/rest/meetings?order=begins_at&limit=3&begins_at=gt.2018-01-17&select=id,title,slug,begins_at'
 ```
Your output should look something like this

```json
[{"id":2,"title":"Course introduction (repeat for shoppers)","slug":"course-introduction-2","begins_at":"2018-01-18T18:00:00+00:00"}, 
 {"id":3,"title":"Version control and the shell","slug":"version-control","begins_at":"2018-01-23T18:00:00+00:00"}, 
 {"id":4,"title":"Relational databases","slug":"relational-databases","begins_at":"2018-01-25T18:00:00+00:00"}]
```

The output is in [JSON](https://www.json.org/) format, which is a common format
for APIs. If you want to make it look pretty, you can pipe it through a program
that will format the JSON. I like to pipe the output into
[jq](https://stedolan.github.io/jq/), but if that is not installed on a system 
I am using, I pipe it to the command `python -mjson.tool`. Here is what the 
out looks like using either of those.

```json
[
  {
    "id": 2,
    "title": "Course introduction (repeat for shoppers)",
    "slug": "course-introduction-2",
    "begins_at": "2018-01-18T18:00:00+00:00"
  },
  {
    "id": 3,
    "title": "Version control and the shell",
    "slug": "version-control",
    "begins_at": "2018-01-23T18:00:00+00:00"
  },
  {
    "id": 4,
    "title": "Relational databases",
    "slug": "relational-databases",
    "begins_at": "2018-01-25T18:00:00+00:00"
  }
]
```

You can see that the class on 1/23 has id "3".

## Find the quiz corresponding to that class.

Meetings are publically visible via the API. The quizzes are not;
you'll need to use your JWT. In the following, I'm going to assume
you put your JWT string into an environment variable called `JWT`
which you can access using syntax like `$JWT` as you could in Bash.

To get a list of quizzes, do

```
curl -H "Authorization: Bearer $JWT" 'https://www.cpsc213.io/rest/quizzes'
```

To get the quiz for this particular class, do the following

```
curl -H "Authorization: Bearer $JWT" 'https://www.cpsc213.io/rest/quizzes?meeting_id=eq.3'
```

Among the fields, you should see an `open_at` field, which tells
you the earliest time that you may begin the quiz. The time is
returned to you with a timezone, so if it says
"2018-01-15T18:00:00+00:00", that means GMT. The `is_open` field
is a boolean that indicates if the quiz is accepting submissions.

Class meetings may have either zero or one quiz. In this case, you
should see that there is a quiz for class meeting with id 3 and output
is something like as follows:

```json
[
  {
    "id": 1,
    "meeting_id": 3,
    "points_possible": 13,
    "is_draft": false,
    "duration": "01:00:00",
    "open_at": "2018-01-15T18:00:00+00:00",
    "closed_at": "2018-01-23T18:00:00+00:00",
    "created_at": "2018-01-16T22:38:13.898804+00:00",
    "updated_at": "2018-01-18T01:54:19.108722+00:00",
    "is_open": true
  }
]
```

You can see that quiz has `id` of "1" and is linked to the meeting
with `id` of "3". In this case `is_open` is true and so we may make a
quiz submission.

## Create a quiz submission.

Once we create a quiz submission, we have a limited time during
which we can submit answers to the quiz. For the quiz above, you
can see the `duration` is one hour. Usually, it will be 20 minutes.

To create a submission, we're going to submit a POST request to
the `/rest/quiz_submissions` API endpoint. We can submit in either
"application/json" content-type or "application/x-www-form-urlencoded".
 Here, I'll show you both. (The API also accepts CSV, which I'm not
 going to show.)

In JSON format, as follows:

```
curl -H "Authorization: Bearer $JWT" -X POST -H "Content-Type: application/json" -d '{"quiz_id": 1}' 'https://www.cpsc213.io/rest/quiz_submissions'
```

In urlencoded form format, as follows:

```
curl -H "Authorization: Bearer $JWT" -X POST -d 'quiz_id=1' 'https://www.cpsc213.io/rest/quiz_submissions'
```


## Show the quiz questions and choices

Now that we have a quiz submission on record, the CSC213 API gives
us permission to see the questions for the quiz. To fetch the questions,
along with the mutliple choice options for each question, use a command
like below:

```
curl -H "Authorization: Bearer $JWT" 'https://www.cpsc213.io/rest/quiz_questions?quiz_id=eq.1&select=id,body,quiz_question_options(id,body)'
```

If you pipe that into jq, or some other JSON-formatting tool, you should
see output like the following.

```json
[
  {
    "id": 1,
    "body": "A distributed version control system allows developers to",
    "quiz_question_options": [
      {
        "id": 1,
        "body": "view a project’s history"
      },
      {
        "id": 2,
        "body": "reverse changes"
      },
      {
        "id": 3,
        "body": "collaborate easily"
      },
      {
        "id": 4,
        "body": "compile code in any language"
      },
      {
        "id": 5,
        "body": "adding meaningful description to changes"
      }
    ]
  },
  {
    "id": 2,
    "body": "Choose the best option to finish this sentence. When you are done with a body of work you should make a git",
    "quiz_question_options": [
      {
        "id": 6,
        "body": "branch"
      },
      {
        "id": 7,
        "body": "tag"
      },
      {
        "id": 8,
        "body": "comment"
      },
      {
        "id": 9,
        "body": "commit"
      }
    ]
  },
  ...
```

(Here the "..." indicates there were other questions I omitted). And,
that's it, those are the questions. You can read more about that query
format in the [PostgREST documentation](https://postgrest.com/en/v4.1/api.html#resource-embedding). 

6. Submit your choice for each question.

For each question, you'll want to mark the options you think are correct
responses to the question. Each question can have multiple correct responses.
To do so, we're going to POST to the `quiz_answers` API endpoint. For question
with `id` 1, the correct options are 1, 2, 3, and 5. To store my selections
I would do the following.

```
curl -H "Authorization: Bearer $JWT" -X POST -d 'quiz_question_option_id=1' 'https://www.cpsc213.io/rest/quiz_answers'
curl -H "Authorization: Bearer $JWT" -X POST -d 'quiz_question_option_id=2' 'https://www.cpsc213.io/rest/quiz_answers'
curl -H "Authorization: Bearer $JWT" -X POST -d 'quiz_question_option_id=3' 'https://www.cpsc213.io/rest/quiz_answers'
curl -H "Authorization: Bearer $JWT" -X POST -d 'quiz_question_option_id=5' 'https://www.cpsc213.io/rest/quiz_answers'
```

(I hope that you see how "scriptable" this is. If you don't write a script, it will
instead be repetitive!)

If you accidently selected an option, you can delete it easily, as such

```
curl -H "Authorization: Bearer $JWT" -X DELETE 'https://www.cpsc213.io/rest/quiz_answers?quiz_question_option_id=eq.4'
```

As you can see in the [API documentation for the `quiz_answers` endpoint](https://www.cpsc213.io/openapi/#/quiz_answers), the `quiz_answer` requires
a `quiz_id` and a `user_id`, but these can be filled in automatically by
the database if you omit them.

## Closing thoughts

Pay attention to the quiz `durations`---you have a limited time to complete
your quiz once you create a quiz submission. All you need to do is use the
API to select your desired answers. We will *likely* create a web interface
for this in the coming weeks. In the meantime, you can use [this shell script](https://gist.github.com/jacobbendicksen/b2845b0c63e279c086eeabd67daf55f8)
written by the TAs, though we make no promises as to its functionality.
