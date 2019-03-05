## 1. Set Checker
I found there are two ways to handle it:

1. The first way for better performance but it's hard to read

```ruby
def set?(*cards)
  # validate parameter
  raise ArgumentError unless cards.length == 3

  # check valid card set
  [:shape, :colour, :fill, :count].each do |attribute|
    unless (cards[0][attribute] == cards[1][attribute] && cards[0][attribute] == cards[2][attribute]) || 
      (cards[0][attribute] != cards[1][attribute] && cards[0][attribute] != cards[2][attribute] && cards[1][attribute] != cards[2][attribute])
      return false
    end
  end

  return true
end
```

2. The second way is better readability

```ruby
def set?(*cards)
  # validate parameter
  raise ArgumentError unless cards.length == 3

  # check valid card set
  [:shape, :colour, :fill, :count].each do |attribute|
    length = [cards[0][attribute], cards[1][attribute], cards[2][attribute]].uniq.length
    return false if length == 2 # it's valid when length == 1 or length = 3, so it isn't valid when length = 2
  end

  return true
end
```

## 2. Domain Modelling

```ruby
# config/routes.rb
Belong::Engine.routes.draw do
  namespace :manager do
    resources :feedbacks, only: %w[index show] do
      resources :conversations, only: %w[create] do
        patch :resolve, on: :member
      end
    end
  end

  namespace :employee do
    resources :feedbacks, only: :show do
      resources :conversations, only: %w[create]
    end
  end
end 
```

### I describe more detail about above routes.
```
As a manager, I can acknowledge that I have seen an open-ended answer.
```
- Path: /manager/feedbacks
  + This page show list of open-ended answers

```
As a manager, I can start a conversation with the employee who wrote the answer.
```
- Path: /manager/feedbacks/:id
  + This page show information of the question, the answer and a form for manager start a conversation
- Path: /manager/feedbacks/:feedback_id/conversations (POST)
  + The conversation form will submit post to that path for create a new conversation

```
As a manager, I can mark a conversation as resolved.
```
- Path: /manager/feedbacks/:feedback_id/conversations/:id/resolve
  + On page /manager/feedbacks/:id will have a button for resolve the conversation. When click the resolve button, a submit will be called to above path set resolved for that conversation.

```
As an employee, I can respond to a conversation with the manager.
```
- Path: /employee/feedbacks/:id
  + This page show information of the question, the answer and the conversation that manager has started. And below having a form for respond the conversation.
- Path: /employee/feedbacks/:feedback_id/conversations (POST)
  + The conversation form will submit post to above path for create a new response

## 3. Bug Fixing

1. At first, I will access to Sentry and check more detail of this issue.
I will find out the causes of this issue such as:
  - Which commit of code that cause it.
  - Where action and which parameters cause it.
  - If I still not find out the reason, I will check more about client request information as:
    + Information of browser: which browser, which version, ...
    + The time that request is sent

2. Next I will checkout that souce code and reproduce on development environment for figure out the reasons of this issue such as:
  + Check logic of source code that it's correct or not
  + Check data in database because maybe some value can cause that issue.

If I still not reproduce the issue, I will share it to my partners or my manager this situation for advice.
At last I will ask for some data on production that perhaps causing problem.

3. After I found the reason, I will fix that issue. I will make some test by myself.
And next, ask for somebody review my code or ask for a test to a QC member.

4. After passed test on development or even staging environment. The fixed commits should be release to production.
And it should be make a test again on prodution. If it's passed that consider this issue "fixed".

## 4. Collaboration
In this case, perhaps I'm not understand clearly about the application because my point is not suitable with the application.
So at first I need learn more detail the other features to understand them by some ways:
  - Ask other collaborator to learn
  - Read documentation (if it's available) and source code of existed features

The next one I will find out the reasons that why my code is not good for existed features.

And next I need to analyze again my new feature and find out some new ways to improve my code for being good with other features.
