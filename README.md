# mamulengo

<img src="https://github.com/wandersoncferreira/mamulengo/blob/master/doc/mamulengo_fuzue.jpg" width=260 align="right"/>

A lightweight database based on
[datascript](https://github.com/tonsky/datascript) and a
pluggable storage for data durability. In fact, `mamulengo`
has probably poor performance when compared to other
solutions and its purpose is small-sized applications that
need an embedded database to get its business moving.


We use [datascript](https://github.com/tonsky/datascript) as
the main database, but write every transaction to a durable
storage. Therefore, because of this simple addition we are
able to provide two features to improve datascript and
enable it to be a full-fledged immutable database:
i)durability and ii) time-travel feature as in Datomic.

Mamulengo supports [PostgreSQL](https://www.postgresql.org/)
and [H2](https://www.h2database.com/html/main.html) database
already!


*Bleedinng-edge project.. Still wondering about APIs and code organization..*


## Usage

```clj
(require '[mamulengo.core :as m])

(def cfg {:durable-storage :postgresql
          :durable-conf {:dbtype "postgresql"
                         :dbname "mamulengo"
                         :password "test"
                         :user "test"}})

(m/connect! cfg)


;;; let's define a schema
(def schema-planets
  {:body/name {:db/cardinality :db.cardinality/one
               :db/unique :db.unique/identity}
   :body/diameter {:db/cardinality :db.cardinality/one
                   :db/unique :db.unique/identity}})

(m/transact-schema! schema-planets)

;;; now you are ready to save your data!!
(m/transact! [{:db/id -1
                     :body/name "Earth"
                     :body/diameter 12740}
                    {:db/id -2
                     :body/name "Pluto"
                     :body/diameter 80}
                    {:db/id -3
                     :body/name "Venus"
                     :body/diameter 12100}])

;;; you should also retrieve it back!
(m/query! '[:find (pull ?e [*])
            :in $ ?n
            :where
            [?e :body/name ?n]]
            ["Pluto"])

```


## Why `mamulengo`?

In Brazilian culture we have a very famous event in the
northeast region called
[mamulengo](https://en.wikipedia.org/wiki/Mamulengo) which
is a puppet theater. In front of the public we have the
puppets that entertain and captivate the audience and behind
it we have the hands of the artist that brings movements and
voice to the puppets.

`Mamulengo` here has the same objectives, we want you to be
amazed by `datalog` query engines and all the bells and
whistle of immutable databases, but behind the scenes we
have so hard work to make this possible with no overhead
introduced to the audience.


## Ideas

The idea came from **Rodolfo Ferreira** about rethinking some
concepts on databases and reaching the conclusion that not all applications need an
insane amount of write/reads per second. Sometimes we only
need a SQLite to do our work and go home happily.

However, we still want the advantages of Datascript and
Immutable data sources.

The very initial code was inspired in  https://gitlab.com/kurtosys/lib/factoidic.


## License

Copyright © 2020 Wanderson Ferreira

This program and the accompanying materials are made available under the
terms of the Eclipse Public License 2.0 which is available at
http://www.eclipse.org/legal/epl-2.0.

This Source Code may also be made available under the following Secondary
Licenses when the conditions for such availability set forth in the Eclipse
Public License, v. 2.0 are satisfied: GNU General Public License as published by
the Free Software Foundation, either version 2 of the License, or (at your
option) any later version, with the GNU Classpath Exception which is available
at https://www.gnu.org/software/classpath/license.html.
