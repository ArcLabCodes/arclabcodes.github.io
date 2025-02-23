---
layout: page
title: 'Angular NgRx Effects Cheat Sheet'
---

#### Overview
When handling side effects in NgRx, you often need to manage multiple asynchronous actions, such as HTTP requests. The choice between `mergeMap`, `concatMap`, and `switchMap` is crucial for controlling how these actions are managed.

- **`mergeMap`**: Used for handling multiple concurrent actions.
- **`concatMap`**: Used for handling actions sequentially, one after the other.
- **`switchMap`**: Used for handling only the latest action, canceling any previous ones.

#### Use Cases

1. **`mergeMap`**: 
   - **When to Use**: When you want to handle multiple actions concurrently without canceling any of them.
   - **Example**: Fetching data from multiple sources or performing multiple independent HTTP requests.
   ```typescript
   @Effect()
   loadThings$ = this.actions$.pipe(
     ofType(LoadThing),
     mergeMap(action =>
       this.httpService.getThing(action.payload).pipe(
         map(data => new LoadThingSuccess(data)),
         catchError(error => of(new LoadThingFail(error)))
       )
     )
   );
   ```

2. **`concatMap`**: 
   - **When to Use**: When you need to process actions in a strict sequence, ensuring one completes before the next starts.
   - **Example**: When order matters, such as sequential updates or ordered data processing.
   ```typescript
   @Effect()
   loadThings$ = this.actions$.pipe(
     ofType(LoadThing),
     concatMap(action =>
       this.httpService.getThing(action.payload).pipe(
         map(data => new LoadThingSuccess(data)),
         catchError(error => of(new LoadThingFail(error)))
       )
     )
   );
   ```

3. **`switchMap`**: 
   - **When to Use**: When you only care about the latest action, canceling any previous unfinished requests.
   - **Example**: Typing in a search box, where only the latest query matters.
   ```typescript
   @Effect()
   loadThings$ = this.actions$.pipe(
     ofType(LoadThing),
     switchMap(action =>
       this.httpService.getThing(action.payload).pipe(
         map(data => new LoadThingSuccess(data)),
         catchError(error => of(new LoadThingFail(error)))
       )
     )
   );
   ```

### Practical Example
For your use case, where you dispatch a `LoadThing` event that triggers an HTTP service and returns either a `LoadThingSuccess` or `LoadThingFail` event, here’s how you can choose between `mergeMap`, `concatMap`, and `switchMap`:

- **`mergeMap`**: If multiple `LoadThing` events can be dispatched simultaneously and should be handled concurrently.
- **`concatMap`**: If `LoadThing` events need to be handled one after another, ensuring each request completes before starting the next.
- **`switchMap`**: If only the latest `LoadThing` event matters, canceling any ongoing requests when a new one is dispatched.

```typescript
@Effect()
loadThing$ = this.actions$.pipe(
  ofType(LoadThing),
  switchMap(action =>
    this.httpService.getThing(action.payload).pipe(
      map(data => new LoadThingSuccess(data)),
      catchError(error => of(new LoadThingFail(error)))
    )
  )
);
```

### Summary
- **`mergeMap`**: Use for concurrent actions.
- **`concatMap`**: Use for sequential actions.
- **`switchMap`**: Use for only the latest action, canceling previous ones.

This cheat sheet should help you quickly decide which mapping operator to use in your NgRx effects based on your specific requirements.