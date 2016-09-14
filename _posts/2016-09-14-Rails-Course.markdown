---
title: Rails Course
layout: post
category: ruby
---

I took the 'Introduction to Ruby on Rails' course on Coursera this spring. I really liked that
the process was essentially self-paced using rspec to know when you had advanced to the next steps.

I stalled about 80% of the way through the second course on 'Rails with Active Record', and was a 
few steps shy of finishing the final assignment in June. 
When it reopened this week, I basically logged in, sat down for four hours, and pushed out the solution, completing the course. The hardest part about the assignment was learning at the very end (during pagination) that the todo_lists_path had really better be / and not /todolists/ which is the default todo_lists_path if you use 'resources todo_lists' for example. 

The other thing I learned is that rspec, and the test db environment, are directly impacted by the size
of the seeds.rb data pool. In the second course, to make sure pagination had something to do,
both 100 todo lists, and 3 todo items per list, were populated. This doesn't sound so bad,
apart from the fact that this was happening on every test. I found tests that ran in a second or two
without seed data taking in excess of one minute to run. Any test that takes more than a few seconds, or any suite that takes longer than 10s, will not become an automatic part of the development process. The instructions in the last assignment have a number of rspec 'checkpoints', after nesting, after setting up authentication, etc. A sizeable delay in the tests was cumbersome, and I cut until the pagination and end-to-end tests the sample size to 10 from 100, which made the test time closer to 6 seconds than 120. 

So since I ploughed through that faster than I thought I might, I started the mongodb and rails course which is the third in their sequence. I also plumped the $79 for the single course certificate, and will need to resubmit the course work with the 'verification' enabled, which seems like it means I need to login from my laptop and use the webcam to smile while I type.

I did get this shiny digital [certificate.](https://www.coursera.org/account/accomplishments/certificate/4F4PTUX5V9SA)