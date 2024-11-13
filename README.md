
# NAME
AnyEvent::ClickHouse - Simple but non-blocking HTTP client for ClickHouse Database

# VERSION
Version 0.031

# SYNOPSIS
```use AnyEvent::ClickHouse;
clickhouse_select
    {   # connection options
        host        => '127.0.0.1',
        port        => 8123,
        user        => 'Harry',
        password    => 'Alohomora',
        database    => 'Hogwarts'
    },
    'select * from test FORMAT Pretty', # query
    sub {   # callback function if status ok 200
        my $data = shift;
    },
    sub {   # callback function if status error 500
        my $data = shift;
    }
;
# ... do something else here
```

# DESCRIPTION
This module is an AnyEvent user, you need to make sure that you use and run a supported event loop.

This module implements a simple and non-blocking HTTP client for ClickHouse Database. It supports clickhouse_do, clickhouse_select clickhouse_select_array and clickhouse_select_hash methods.

# DEFAULTS
```# connection options
{
        host        => '127.0.0.1',
        port        => 8123
        database    => 'default'
}
```

# BASIC METHODS
### clickhouse_select
Fetch data from the table (readonly).

By default, data is returned in TabSeparated format. You use the FORMAT clause of the query to request any other format.

The supported formats for 'select' are:

| Format |
| - |
| TabSeparated |
| TabSeparatedRaw |
| TabSeparatedWithNames |
| TabSeparatedWithNamesAndTypes |
| TabSeparatedRawWithNames |
| TabSeparatedRawWithNamesAndTypes |
| Template |
| CSV |
| CSVWithNames |
| CSVWithNamesAndTypes |
| CustomSeparated |
| CustomSeparatedWithNames |
| CustomSeparatedWithNamesAndTypes |
| SQLInsert |
| Values |
| Vertical |
| JSON |
| JSONStrings |
| JSONColumns |
| JSONColumnsWithMetadata |
| JSONCompact |
| JSONCompactStrings |
| JSONCompactColumns |
| JSONEachRow |
| PrettyJSONEachRow |
| JSONEachRowWithProgress |
| JSONStringsEachRow |
| JSONStringsEachRowWithProgress |
| JSONCompactEachRow |
| JSONCompactEachRowWithNames |
| JSONCompactEachRowWithNamesAndTypes |
| JSONCompactStringsEachRow |
| JSONCompactStringsEachRowWithNames |
| JSONCompactStringsEachRowWithNamesAndTypes |
| JSONObjectEachRow |
| BSONEachRow |
| TSKV |
| Pretty |
| PrettyNoEscapes |
| PrettyMonoBlock |
| PrettyNoEscapesMonoBlock |
| PrettyCompact |
| PrettyCompactNoEscapes |
| PrettyCompactMonoBlock |
| PrettyCompactNoEscapesMonoBlock |
| PrettySpace |
| PrettySpaceNoEscapes |
| PrettySpaceMonoBlock |
| PrettySpaceNoEscapesMonoBlock |
| Prometheus |
| Protobuf |
| ProtobufSingle |
| ProtobufList |
| Avro |
| Parquet |
| Arrow |
| ArrowStream |
| ORC |
| Npy |
| RowBinary |
| RowBinaryWithNames |
| RowBinaryWithNamesAndTypes |
| Native |
| Null |
| XML |
| CapnProto |
| LineAsString |
| RawBLOB |
| MsgPack |
| Markdown |

https://clickhouse.com/docs/en/interfaces/formats

```
clickhouse_select
    {
        host        => '127.0.0.1',
        user        => 'Harry',
        password    => 'Alohomora'
    },
    'select * from test',
    sub {   # callback function if status ok 200
        my $data = shift;
        # ... do something
        # print $data;
    },
    sub {   # callback function if status error 500
        my $data = shift;
        # do something
        # print "err: ".$data."\n";
    }
;
```

You can use URI

```
use URI;
use URI::QueryParam;
my $uri = URI->new(sprintf ("http://%s:%d/",'127.0.0.1',8123));  # host and port
$uri->query_param('user' => 'Harry');
$uri->query_param('password' => 'Alohomora');
$uri->query_param('database' => 'default');
clickhouse_select
    { 
        uri => $uri
    },
    ...
```

### clickhouse_do
Universal method for any queries inside the database, which modify data (insert data, create, alter, detach or drop table or partition).

```clickhouse_do
    {
        host        => '127.0.0.1',
        user        => 'Harry',
        password    => 'Alohomora'
    },
    "INSERT INTO test (id, f1, f2) VALUES",
    sub {   # callback function if status ok 200
        my $data = shift;
        # do something
    },
    undef,   # You can skip callback function
    [1, "Gryffindor", "a546825467 1861834657416875469"],
    [2, "Hufflepuff", "a18202568975170758 46717657846"],
    [3, "Ravenclaw", "a678 2527258545746575410210547"],
    [4, "Slytherin", "a1068267496717456 878134788953"]
;
```

# ADDITIONAL METHODS

### clickhouse_select_array
Fetch data from the table (readonly). It returns a reference to an array that contains a references to an arrays for each row of data fetched.

Don't use FORMAT in query!

```
clickhouse_select_array
    {
        host        => '127.0.0.1',
        user        => 'Harry',
        password    => 'Alohomora'
    },
    'select * from test',
    sub {   # callback function if status ok 200
        my $data = shift;
        # ... do something
        # foreach my $row (@$data) {
        #     # Do something with your row
        #     foreach my $col (@$row) {
        #         # ... Do something
        #         print $col."\t";
        #     }
        #     print "\n";
        # }
    },
    sub {   # callback function if status error 500
        my $data = shift;
        # do something
        # print "err: ".$data."\n";
    }
;
```

### clickhouse_select_hash
Fetch data from the table (readonly). Returns a reference to an array that contains a hashref to the names of the columns (as keys) and the data itself (as values).

Don't use FORMAT in query!

```
clickhouse_select_hash
    {
        host        => '127.0.0.1',
        user        => 'Harry',
        password    => 'Alohomora'
    },
    'select * from test',
    sub {   # callback function if status ok 200
        my $data = shift;
        # ... do something
        # foreach my $row (@$data) {
        #     # Do something with your row
        #     foreach my $key (keys %{$row}){
        #         # ... Do something
        #         print $key." = ".$row->{$key}."\t";
        #     }
        #     print "\n";
        # }
    },
    sub {   # callback function if status error 500
        my $data = shift;
        # do something
        # print "err: ".$data."\n";
    }
;
```

# SEE ALSO
[ClickHouse official documentation](https://clickhouse.com/docs)

[AnyEvent](https://metacpan.org/pod/AnyEvent)

[AnyEvent::Log](https://metacpan.org/pod/AnyEvent::Log)

# AUTHOR
Maxim Motylkov

# MODIFICATION HISTORY
See the Changes file.

# COPYRIGHT AND LICENSE
Copyright 2016 Maxim Motylkov


```
This module is free software, you can redistribute it and/or modify it under the same terms as Perl itself. 

THIS PACKAGE IS PROVIDED "AS IS" AND WITHOUT ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, WITHOUT LIMITATION,
THE IMPLIED WARRANTIES OF MERCHANTIBILITY AND FITNESS FOR A PARTICULAR PURPOSE.
```
