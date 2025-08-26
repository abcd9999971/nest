---
title: TypeScript筆記 as const
date: 2025-04-08 11:31:09
tags:
---

First, I make a type <TodoStyle>.

```ts
export type TodoStyle  = {
    id: number;
    title: string;
    date: string;
    isCompleted: boolean;    
};
```
Then, I use react-hook-from to register new todo into DOM.
I use TodoStyle as the type of these data.


```ts
      console.log(watch("title")) // watch input value by passing the name of it

      return (
        <form onSubmit={handleSubmit(onSubmit)}>
          <input {...register("title", { required: true })} />
          {errors.title && <span>This field is required</span>}
          
          <button type="submit"/>
        </form>
      )
```
then comes the problem

<b>First</b> 
Are these two watch("title") and <input {...register("title", { required: true })} />
same stuff?

<b>Second</b>
Why the from-hook know which place is the input'title' should put in? 

AI gave me a good solution. Make a as const const to protect the names.

```ts
const FIELD_NAMES = {
  title: "title",
  date: "date",
  isCompleted: "isCompleted"
} as const;
```