# New @If syntax

This project was generated with [Angular CLI](https://github.com/angular/angular-cli) version 17.0.3.

[Angular @if: Complete Guide](https://blog.angular-university.io/angular-if/)

## Why don't we need to import @if?

Notice that we don't have to **import** the `@if` directive from `@angular/common` into our component templates anymore.
This is because the `@if` syntax **is part of the template engine** itself, and it is **not a directive**.
The new `@if` is **built-in directly into the template engine**, so it's automatically *available everywhere*.
It's just like the `{{ variable }}` interpolation syntax or the i18n syntax - you don't need to import anything in order to use it.

## Why don't we need to use the * syntax anymore?

The bottom line is that with `@if` we don't need to use the `*` syntax anymore, because `@if` is **not a structural directive**.

## How to easily migrate to the new @if syntax?

The Angular CLI has an automated migration for upgrading all your code to the new `@if` syntax:

```bash
ng generate @angular/core:control-flow
```

This command will migrate all the `*ngIf` directives in your project to the new syntax, not only for `@if` but also for the `@for` and `@switch` syntax.

## Multiple nested @if async pipe anti-pattern

This anti-pattern is sometimes known as "async pipe chaining" or "pyramid of doom".

Here is an example of this `@if` anti-pattern:

```html
@if (user$ | async; as user) {
    ....
    @if (course$ | async; as course) {
        ....
        @if (lessons$ | async; as lesson) {
            ....
        }
    }
}
```

A good way to avoid this is to **refactor** your component so that it provides **one single** `data$` observable, containing **all** the data that the page needs.
Start by defining one interface that contains all the data that the page needs:

```typescript
interface PageData {
    user: User;
    course: Course;
    lessons: Lesson[];
}
```

Then, define a single `data$` observable that contains **all** the data that the page needs:

```typescript
@Component
export class Component implements OnInit {
    
  private data$: Observable<PageData>;

  ngOnInit() {
    
    const user$ = // ... initialize user$ observable

    const course$  = // ... initialize course$ observable

    const lessons$ = // ... initialize lessons$ observable    
    
    this.data$ = combineLatest([user$, course$, lessons$])
      .pipe(
        map(([user, course, lessons]) => {
          return {
                user, 
                course, 
                lessons
            }
        })
    );
  }
}
```

Finally, use `@if` in combination with the async pipe to unwrap the `data$` observable in the template:

```html
@if (data$ | async; as data) {
    ....
    {{ data.course }}

    {{ data.user }}

    {{ data.lessons }}
}
```
