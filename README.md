# Monfield

```
================================================================

           ┏┳┓┏━┓┏┓╻┏━╸╻┏━╸╻  ╺┳┓   ┏┳┓╻┏━╸┏━┓┏━┓╺┳╸┏━╸
           ┃┃┃┃ ┃┃┗┫┣╸ ┃┣╸ ┃   ┃┃   ┃┃┃┃┃╺┓┣┳┛┣━┫ ┃ ┣╸
           ╹ ╹┗━┛╹ ╹╹  ╹┗━╸┗━╸╺┻┛   ╹ ╹╹┗━┛╹┗╸╹ ╹ ╹ ┗━╸

                   Mongodb Field Migration Tool

    AUTHOR:     Mohammad Matini <mohammad.matini@outlook.com>
    MAINTAINER: Mohammad Matini <mohammad.matini@outlook.com>
    COPYRIGHT:  Workiom Inc. (2020)

================================================================
```

Monfield is a tool for automating the update of a specific field in specific
record. The fields to query by and to update are read from a CSV file, and
the updates are executed on a remote mongo instance.

## Dependencies
Monfield requires the presence of the MongoDB CLI Client to execute database
update operations. Please make sure that your mongo client version is in
sync with your database server version.

## Usage
```
USAGE:

    monfield [REQUIRED-ARGUMENTS] [OPTIONS]


DESCRIPTION:

    monfield is a tool for automating the update of a specific field in
    specific record. The fields to query by and to update are read from
    a CSV file, and the updates are executed on a remote mongo instance.


REQUIRED ARGUMENTS:

    --hostname                      URL for the database, as host:port
    --authentication-database       authentication database
    --username                      username to authenticate as
    --database-name                 database in which the migration will run
    --collection-name               collection in which the migration will run
    --migration-file                CSV file that contains the field definitions


OPTIONS:

    -h, --help                      print this help message
    --dryrun                        print the mongo scripting calls instead
                                    of executing them
    --query-value-constructor       constructor name to be called on every
                                    query value, e.g. passing ObjectId will
                                    call ObjectId("query-value") on query
                                    values (Not passing this option means
                                    that all query values are passed
                                    literally)
    --update-value-constructor      constructor name to be called on every
                                    update value e.g. passing ISODate will
                                    call ISODate("update-value") on update
                                    values (Not passing this option means
                                    that all query values are passed
                                    literally)


MIGRATION FILE:

    The migration file is a CSV file which is structured as follows:

        query-field,update-field
        query-value,update-value
        query-value,update-value
        query-value,update-value
        ...

    For each row containing a [query-value]-[update-value] pair, an
    update is executed; we find records where [query-field] is
    [query-value] and set their [update-field] to [update-value].
```

## Examples
### Update a Date Field in Records by _id
This CSV file contains pairs of _id and a date field. The first row declares
the field IDs. The rest of the rows are query-value and update-value
fields. Monfield will find records by their _id in the first column, and
will update their date field with the new date specified in the second
column.

```csv
_id,156231
5f5de62929bea0000119407a,2015-05-01T01:01:12Z
5f51685c640003000170dd93,2014-05-01T01:01:12Z
5f3a9b8534847200011774f6,2013-05-01T01:01:12Z
5f39a77f6fc9b20001dde8db,2012-05-01T01:01:12Z
5f39505e0bf7090001ddd668,2011-05-01T01:01:12Z
```

To execute this migration call monfield like:

```sh
monfield --hostname localhost:27017 --authentication-database authdb --username admin --database-name 121 --collection-name b0a1f011-423a-46fa-3e7c-0u27b581b917 --migration-file ./updates.csv --query-value-constructor ObjectId --update-value-constructor ISODate
```

### Update a Date Field in Records by a Text Field
This CSV file contains pairs of strings from a name field, and dates from a
date field. The first row declares the field IDs. The rest of the rows are
query-value and update-value fields. Monfield will find records by the value
of their name in the first column, and will update their date field with the
new date specified in the second column.

```csv
156232,156231
My Soul, Your Beats!,2018-05-01T01:01:12Z
Brain Freeze,2016-05-01T01:01:12Z
Fly Me To The Moon,2011-05-01T01:01:12Z
Komm, Süsser Tod,2011-02-01T01:01:12Z
Hell March 2,2010-02-01T01:01:12Z
```

To execute this migration call monfield like:

```sh
monfield --hostname localhost:27017 --authentication-database authdb --username admin --database-name 121 --collection-name b0a1f011-423a-46fa-3e7c-0u27b581b917 --migration-file ./updates.csv --update-value-constructor ISODate
```

Note that unlike in the previous example, we don't specify the ObjectId
query-value-constructor. This is because the first field this time is a
normal text field, and does not require a call to ObjectId(). We still
specify the ISODate update-value-constructor because our update values in
the second column are date values.

## License
Copyright (C) 2020 Workiom Inc.

Monfield is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

Monfield is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with Monfield. If not, see <https://www.gnu.org/licenses/>.
