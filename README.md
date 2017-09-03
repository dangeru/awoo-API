# awoo-API
API v2 for Awoo / danger/u/

### Boards

To get a list of boards, poll `/api/v2/boards`, which will return a JSON encoded list of strings.

If you are authenticated, the list of boards will include hidden boards like /staff/

### Indexes

To get the threads on a board, poll `/api/v2/board/:board`. This will return an array of the 20 most recently bumped threads as metadata hashes, which are documented below

To get another page, add a parameter like this: `/api/v2/board/:board?page=1` to get the second page, etc..

### Thread information

To get the metadata hash for a thread, poll `/api/v2/thread/:id/metadata`

### Thread replies

To get the replies to a thread, poll `/api/v2/thread/:id/replies`. This will return an array of metadata hashes, starting with the OP.

## Metadata hashes

A metadata hash always has these keys:

 - `post_id` (integer)
 - `board` (string)
 - `is_op` (boolean)
 - `comment` (string)
 - `date_posted` (integer)
 - `hash` (string)

`hash` is the poster's ID, a part of the hash of the poster's IP address and the OP's thread id

If the request is authenticated and you moderate the board of the requested thread, the `ip` will be in the hash as well.

If the thread has an associated capcode, there will be a `capcode` key as well, with just the name of the janitor

If the post is a reply, the `parent` key will be the post id of the OP.

Otherwise, these keys will be present

 - `title` (string)
 - `last_bumped` (integer)
 - `is_locked` (boolean)
 - `number_of_replies` (integer)
 - `sticky` (boolean)

If `sticky` is set to true, the `stickyness` key will also be in the result set, an integer where a higher value represents a higher priority in the board list.

For a reference decoder and some example usage, see `API.Thread` and `API.WatchableThread` in the [la/u/ncher](https://github.com/nilesr/United4) android app

## Authentication

To login, send a POST request to `/mod` with the `username` and `password` keys set. If successful, the response code will be a 303. If the username or password are incorrect, the response code will be a 403. 

On success, a `Set-Cookie` header will be returned. Simply forward that cookie along with further requests and they will be authenticated. The 
`API.Authorizer` class in the [la/u/ncher](https://github.com/nilesr/United4) android app shows an example implementation that can be used to login once and allow other requests to be automatically authenticated

## Posting

To make a new OP, send the `board`, `title` and `comment` parameters in a POST request to `/post`.  
If the request is authenticated and the `capcode` parameter is also specified, the post will be made with the capcode of the authenticated janitor.

To make a new reply, send the `board`, `parent` and `content` parameters in a POST request to `/reply`.  
If the request is authenticated and the `capcode` parameter is also specified, the post will be made with the capcode of the authenticated janitor.
