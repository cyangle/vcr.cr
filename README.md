# vcr [![Build Status](https://travis-ci.org/spoved/vcr.cr.svg?branch=master)](https://travis-ci.org/spoved/vcr.cr)

VCR for Crystal!

Record your test suite's HTTP interactions and replay them during future test runs for fast, deterministic, accurate tests.

Example reduction in test time with over 2k RESTful requests:

#### Without VCR
```
Finished in 10:22 minutes
18 examples, 0 failures, 0 errors, 0 pending
```

#### With VCR
```
Finished in 13.05 seconds
18 examples, 0 failures, 0 errors, 0 pending
```

#### Notes:
The idea of this port is to keep it simple and allow multiple requests to be recorded within a single "cassette" block. The ability to record changed requests to the same endpoint is also important and unavailable in other vcr ports. I have also tried to keep the syntax and methods as close to the Ruby VCR to help.

Other VCR like ports:
* [hi8](https://github.com/vonKingsley/hi8.cr)
* [eighttrack](https://github.com/russ/eighttrack)


## Installation

Add this to your application's `shard.yml`:

```yaml
development_dependencies:
  vcr:
    github: spoved/vcr.cr
```

## Usage

```crystal
require "vcr"
require "http/client"

load_cassette("cassette-one") do
  response = HTTP::Client.get("https://jsonplaceholder.typicode.com/todos/1")
end
```

You can also record multiple requests within a single block:

```crystal
load_cassette("cassette-two") do
  r1 = HTTP::Client.get("https://jsonplaceholder.typicode.com/todos/1")
  r2 = HTTP::Client.get("https://jsonplaceholder.typicode.com/todos/2")
end
```

To easily reset the cassette and record, simply add the `:record` argument:

```crystal
load_cassette("cassette-two", :record) do
  r1 = HTTP::Client.get("https://jsonplaceholder.typicode.com/todos/1")
  r2 = HTTP::Client.get("https://jsonplaceholder.typicode.com/todos/2")
end
```

To record difference in the same response add the `:in_order` argument. This Will
record and play back the VCR in order the requests occurred (recording new ones if missing).

```crystal
VCR.use_cassette("cassette-one", :record, :in_order) do
  r1 = HTTP::Client.get("https://jsonplaceholder.typicode.com/todos")
  HTTP::Client.delete("https://jsonplaceholder.typicode.com/todos/1")
  r2 = HTTP::Client.get("https://jsonplaceholder.typicode.com/todos")
end
```

Customize the location of where the cassettes are stored. The default is `spec/fixtures/vcr`.

```crystal
VCR.configure do
  settings.cassette_library_dir = "/some/path/cassettes"
end
```

### Spec usage

Add a hook to load a cassette before the whole suite or use `before_each` inside your context

```crystal
Spec.before_suite { load_cassette("my-spec-cassette", :record) }
```

## Contributing

1. Fork it (<https://github.com/spoved/vcr.cr/fork>)
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create a new Pull Request

## Contributors

- [kalinon](https://github.com/kalinon) Holden Omans - creator, maintainer
