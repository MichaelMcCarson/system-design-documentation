# Function Location

## The Step Down Rule

We want the code to read like a top-down narrative. We want every function to be followed by those at the next level of abstraction so that we can read the program, descending one level of abstraction at a time as we read down the list of functions.

[The Stepdown Rule](https://marekhudyma.com/code-style/2021/03/02/step-down-rule.html)

## Caller vs Callee

When reading a file or class or block of code, it should read from the top down; that means that a function that calls another function ('caller') should be directly above the function that is called ('callee') except when it has already been declared previously in the file. For more details, see ["What is the difference between callee and caller?"](https://quick-adviser.com/what-is-callee-method/#What_is_the_difference_between_callee_and_caller).

```ts
export function addPersistentCookie(cookie: Cookie): void {
  if (!isExpired(cookie)) {
    return;
  }

  const currentCookiesCount = self.allCookies.length();
  self.allCookies[currentCookiesCount] = cookie;
}

function isExpired(cookie: Cookie): boolean {
  const currentTimeInMilliseconds = new Date().getTime(); // See (*) comment below
  const isCookieExpired = cookie.expires < currentTimeInMilliseconds;
  return isCookieExpired;
}
```

## File Structure

Additionally, file structure should be consistent across files. Constants, public or private members, functions and so on should be located in the same place.
