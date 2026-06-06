## channels

|actions | open                             | closed                                            | nil          |
|--------|----------------------------------|---------------------------------------------------|--------------|
|read    | pause until something is written | return 0 value (use comma ok to see if closed)    | hang forever |
|write   | pause until something is read    | panic                                             | hang forever |
|close   | works                            | panic                                             | panic        |
