---
layout: post
title:  "Estimation with Cynefin Framework"
date:   Sat Jan 22 18:57:57 CET 2022
categories: pm
---

While estimating your backlog, you probably faced a situation when your team members had a different idea of what story points represented. Some see it as a certain number of hours per story point, and others see it as one or several days. However, binding to time is challenging, even for experienced engineers.

Sometimes we like to use "shirt size" to group tasks by complexity. But something that can seem complex for one person is not necessarily as complex for another. And coming to an agreement on the level of complexity for each shirt size (S, M, L, XL) can be tricky.

However, we could find a better way of estimating if we look at **how much we don't know** about a task instead of assessing how complex it is.

## Cynefin

[Cynefin](https://thecynefin.co/about-us/about-cynefin-framework) is a decision-making framework that helps organize systems or problems by complexity. Let's look at the Cynefin categorization types.

- **Simple** (*known - knowns*) - best practices
- **Complicated** (*known - unknowns*) - good practices
- **Complex** (*unknown - unknowns*) - emerging practices
- **Chaos** (*unknowable - unknowables*) - novel practice
- **Disorder** - the domain of the problem at hand is unexplored

It offers five categories, starting with Simple, in which operations and procedures are standardized and proven to work. That can be our starting point in establishing foundation for estimation.

## Ground rules

Let's skip disorder for the sake of simplicity and define ground rules in the other four categories while building on how much we don't know about a task.

 - We define a **simple** task as a task that doesn't have unknowns. Implementation of such a task is merely following a step-by-step instruction that must be outlined in the ticket.
    
 - A **complicated** task has a step-by-step plan, but some steps (at least one) are unclear and need research.

 - There is either no step-by-step plan for a **complex** task or the plan is vague. Research is needed upfront. But there is a more or less clear understanding of what it should look like when it is done.

 - It is a **chaotic** assignment when there is no clear definition of what we want to achieve, and it's a new field that needs studying, or it's hard to comprehend the idea.

As an example, you can think of [adding an IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html#id_users_create_console), which is most likely a standardized number of steps in any organization and, therefore, a simple task. Defining a policy for an IAM role might be seen as a complicated task. Though the process requires known actions, you might need to look up necessary policy rules that you don't remember off the top of your head to build certain restrictions.

In the follow up article, I will show more examples of tasks in different categories and talk about how these ground rules can be mapped onto estimation story points.

---

## Reference

[https://www.frednh.org/combine-cynefin-and-fibonacci-to-estimate](https://www.frednh.org/combine-cynefin-and-fibonacci-to-estimate)
