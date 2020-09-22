## Daily Practice
### Tuesday, September 22nd, 2020
---


# [How Do I](https://laracasts.com/series/how-do-i)


## [How Do I Use Iterators](https://laracasts.com/series/how-do-i/episodes/3)

When you iterate over an _object_, you iterate over the public properties (this is rarely useful).

Other things that can be iterated:
- database queries
- scanning a directory
- reading lines in a file

[PHP already provides many custom iterator classes](https://www.php.net/manual/en/spl.iterators.php):
- `ArrayIterator`  
- `DirectoryIterator`  
- `FilesystemIterator`  
- etc.

`Iterator` interface requires 5 different methods to be defined:
- current() - what's the current item?
- next() - what's the next item?
- key() - what's the key of the item?
- rewind() - how does the internal pointer get reset back to the first item?
- valid() - are there more items to iterate over?

`IteratorAggregate` only requires one method (`getIterator()`) to be defined  

`Collection` class implements `IteratorAggregate` interface  

Example of class that implements `IteratorAggregate`:
```
class Collection implements IteratorAggregate {

    protected $items;

    public function __construct($items) {
        $this->items = $items;
    }

    public function getIterator() {
        return new ArrayIterator($this->items);
    }
}
```

Example of class that implements `Iterator`:
```
class Collection implements Iterator {

    protected $items;

    public function __construct($items) {
        $this->items = $items;
    }

    public function current() {
        return current($this->items);
    }

    public function next() {
        return next($this->items);
    }

    public function key() {
        return key($this->items);
    }

    public function rewind() {
        return reset($this->items);
    }

    public function valid() {
        return !! current($this->items);
    }
}
```

Most of the time, implementing `Iterator` is overkill: use if you need fine-grain control over data and how it is iterated  

Implementing `IteratorAggregate` is much more common (and simple)  



## [How Should I Name My Events and Listeners?](https://laracasts.com/series/how-do-i/episodes/4)

Events can be named (in the past-tense) as events that take place within the app.
- Examples: `UserRegistered`, `LessonWasPublished`, `UserRepliedToConversation`, etc.

Listeners can be named as to-do items/procedures/actions/verbs that should happen as a result of the Event.
- Examples: `SendWelcomeEmail`, `ScheduleThirtyDayFollowupEmail`, `AddUserToNewsletter`, etc.

Good idea to group Events and Listeners into directories based on what they deal with (such as "Registration")
- Example: `App\Events\UserRegistered` --> `App\Events\Registration\UserRegistered`

If an action doesn't feel right to have as part of the main core logic, then create an Event Listener for it
- Example - when the `UserRepliedToConversation` event occurs:
   * The core logic is:
      - create a new record in `replies` table
      - associate the new record with the user that created it
      - associate the new record with the post that it is on
   * The side-effects are:
      - email conversation subscribers (`EmailConversationSubscribers` listener)
      - email any mentioned users (`EmailMentionedUsers` listener)
