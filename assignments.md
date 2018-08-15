# Assignments

## About the assignments

The class assignments partition into two sets: skill building
assignments that are completed individually, and a class project
that is completed in a small team employing your new technical and
management skills.

In the skill building homework assignments, you will develop an
understanding of [HTML5](http://en.wikipedia.org/wiki/HTML5),
[JavaScript](http://en.wikipedia.org/wiki/JavaScript),
[CSS](http://en.wikipedia.org/wiki/Cascading_Style_Sheets), and
[git](http://git-scm.com/). You will also familiarize yourself with
various services that we will use during the semester including
[GitHub](https://github.com/), [Heroku](http://heroku.com/), and
[Cloud9](https://c9.io/). These assignments will be completed
individually and will be submitted through the class website.

## Submitting through the class website

On the class website there is an "Assignments" link. From there, you
can browse individual assignments including instructions for completing
and submitting each assignment. Most persons will want to submit their
assignments through the class website.

## Submitting through the class API

You'll submit assignments through the class API. The relevant API
endpoints are

As with most API requests, you'll need a JSON Web Token, which you
can get from your [class dashboard](https://www.cpsc213.io/#/dashboard).
See the [quiz submission instructions](quizzes.md) for how to get that
into your `JWT` environment.

I will show you here how to submit the [cli and git](https://www.cpsc213.io/#assignments/cli-and-git)
homework assignment.

First, let's find the relevant assignment using the API.

```
curl -H "Authorization: Bearer $JWT" 'https://www.cpsc213.io/rest/assignments?slug=eq.cli-and-git'
```

This should show output in JSON format akin to the following

```json
[
  {
    "slug": "cli-and-git",
    "points_possible": 30,
    "is_draft": false,
    "is_markdown": false,
    "is_team": false,
    "title": "CLI and git",
    "body": "Learn some basic command line skills and how to work with the git version control system\n\nIn this assignment you will complete a series of exercises that expose you to common shell commands and git workflows.\nYou'll be manipulating files and directories and you'll end up producing a git repository that you will push up to GitHub.\nTo begin, you will need an account on [GitHub](http://github.com). Next, you will need to accept the GitHub Assignment\nInvite to get the assignment starter code.\n\nThe link is [https://classroom.github.com/a/b5p8t1HK](https://classroom.github.com/a/b5p8t1HK).\n\nWhen you accept that invitation, GitHub will fork a copy of our starter code for you. You'll make\nchanges to that code and then push it back up to GitHub. There are instructions on how to complete the assignment in\nthe `README.md` file of the starter code. \n\nFinally, I made a [short video for those of you who would like some help getting started](https://www.youtube.com/watch?v=qHTlK68cQ5E).",
    "closed_at": "2018-01-24T04:59:59+00:00",
    "created_at": "2018-01-16T22:21:05.60936+00:00",
    "updated_at": "2018-01-22T15:24:47.754189+00:00",
    "is_open": true
  }
]
```

You can, of course, select only the fields you need to see in the API query, like

```
curl -H "Authorization: Bearer $JWT" 'https://www.cpsc213.io/rest/assignments?slug=eq.cli-and-git&select=slug,is_open'
```

Now, let's see what "fields" are required for this assignment.

```
curl -H "Authorization: Bearer $JWT" 'https://www.cpsc213.io/rest/assignment_fields?assignment_slug=eq.cli-and-git'
```

The output should be similar to the following

```json
[
  {
    "id": 1,
    "assignment_slug": "cli-and-git",
    "label": "Your GitHub repo URL",
    "help": "This should have been automatically created for you when you accepted the GitHub classroom invite.",
    "placeholder": "https://github.com...etc",
    "is_url": true,
    "is_multiline": false,
    "display_order": 0,
    "created_at": "2018-01-16T22:21:05.67306+00:00",
    "updated_at": "2018-01-16T22:21:05.67306+00:00"
  }
]
```

There is only a single field and it has `id` of 1.

To submit the assignment, we need to create an
[assignment submission](https://www.cpsc213.io/openapi/#/assignment_submissions/post_assignment_submissions) and then
submit each of the fields. As with quizzes, we do this with a POST request.
To create an assignment submission, we'll need our user id, which you can find
in [class dashboard](https://www.cpsc213.io/#/dashboard). In my example case,
the user id is 66. I'm going to create an assignment submission for this
assignment as follows:

```
curl -X POST -H "Authorization: Bearer $JWT" -d 'user_id=66&assignment_slug=cli-and-git' 'https://www.cpsc213.io/rest/assignment_submissions'
```

I could have done the same thing using the `application/json` content type as follows:

```
curl -X POST -H "Content-Type: application/json" -H "Authorization: Bearer $JWT" -d '{"user_id": 66, "assignment_slug": "cli-and-git"}' 'https://www.cpsc213.io/rest/assignment_submissions'
```

You can [read more about POSTing using curl here](https://gist.github.com/subfuzion/08c5d85437d5d4f00e58).

Next I need to fill out the assignment fields. In this case, there is just one.
Before doing so, I need to determine the `id` of the assignment submission I just
created. I can find that as such

```
 curl -H "Authorization: Bearer $JWT"  'https://www.cpsc213.io/rest/assignment_submissions'
```

It returns just one row for me that looks like this:

```json
[
  {
    "id": 3,
    "assignment_slug": "cli-and-git",
    "is_team": false,
    "user_id": 66,
    "team_nickname": null,
    "submitter_user_id": 66,
    "created_at": "2018-01-22T16:05:38.218648+00:00",
    "updated_at": "2018-01-22T16:05:38.218648+00:00"
  }
]
```

My repo URL on GitHub is "https://github.com/yale-cpsc-213-spring-2018/assignment_cli-and-git-janeqyalie".
I'll create the
[assignment field submission](https://www.cpsc213.io/openapi/#/assignment_field_submissions/post_assignment_field_submissions)
with the following request, using the
`application/json` content type

```
curl -X POST -H "Content-Type: application/json" -H "Authorization: Bearer $JWT" -d '{"assignment_submission_id": 3, "assignment_field_id": 1, "body": "https://github.com/yale-cpsc-213-spring-2018/assignment_cli-and-git-janeqyalie"}' 'https://www.cpsc213.io/rest/assignment_field_submissions'
```

As you can [read more about here](https://gist.github.com/subfuzion/08c5d85437d5d4f00e58), I could
have put that JSON content in a file and sent it in the POST body, instead of writing it on
the command line.

To see all your assignment submissions and their fields, do the following:

```
curl -H "Authorization: Bearer $JWT" 'https://www.cpsc213.io/rest/assignment_submissions?select=id,assignment_slug,assignment_field_submissions(body)'
```

And, that's it. We created the assignment submission and filled in the assignment
fields that were required. A few thoughts:

- Unlike quizzes, there is no timer once you start an assignment submission. You just
  need to get it in before the assignment is due ("closes_at" in the assignment API).
- Some assignments will have multiple fields. Though, many will just require a git repo
  where we can find our code. If you created that repo using one of the GitHub Classroom
  invites that we often use, then the teaching staff will have the read permissions
  needed to grade your assignment submission.
- Later in the semester, many of the assignments are team submissions. In those cases,
  you'll create an assignment submission using your team nickname instead of your user id.
  And, all team members will have read/write access to those assignments. With individual
  assignments, you have read/write only to your own assignment submissions.
