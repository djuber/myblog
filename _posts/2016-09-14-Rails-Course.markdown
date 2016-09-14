---
title: Rails Course
layout: post
category: ruby
---

I took the 'Introduction to Ruby on Rails' course on Coursera this spring. I really liked that
the process was essentially self-paced using rspec to know when you had advanced to the next steps.

I stalled about 80% of the way through the second course on 'Active Record', and was a few steps shy of 
finishing the final assignment in June. When it reopened this week, I basically logged in, sat down for four hours, and pushed out the solution, completing the course. If only I cared to pay the $79 for a statement of completion, I could share that with the world. The hardest part about the assignment was learning at the very end (during pagination) that the todo_lists_path had really better be / and not /todolists/ which is the default todo_lists_path if you use 'resources todo_lists' for example. 

The other thing I learned is that rspec, and the test db environment, are directly impacted by the size
of the seeds.rb data pool. In the second course, to make sure pagination had something to do,
both 100 todo lists, and 3 todo items per list, were populated. This doesn't sound so bad,
apart from the fact that this was happening on every test. I found tests that ran in a second or two
without seed data taking in excess of one minute to run. Any test that takes more than a few seconds, or any suite that takes longer than 10s, will not become an automatic part of the development process. The instructions in the last assignment have a number of rspec 'checkpoints', after nesting, after setting up authentication, etc. A sizeable delay in the tests was cumbersome, and I cut until the pagination and end-to-end tests the sample size to 10 from 100, which made the test time closer to 6 seconds than 120. 

