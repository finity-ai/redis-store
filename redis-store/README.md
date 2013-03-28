# Redis stores for Ruby frameworks

__Redis Store__ provides a full set of stores (*Cache*, *I18n*, *Session*, *HTTP Cache*) for all the modern Ruby frameworks like: __Ruby on Rails__, __Sinatra__, __Rack__, __Rack::Cache__ and __I18n__. It natively supports object marshalling, timeouts, single or multiple nodes and namespaces.

This is the core for all the other gems, please check the *READMEs* to be informed about the usage.

## Redis Installation

### Option 1: Homebrew

MacOS X users should use [Homebrew](https://github.com/mxcl/homebrew) to install Redis:

    brew install redis

### Option 2: From Source

Download and install Redis from [http://redis.io](http://redis.io/)

	wget http://redis.googlecode.com/files/redis-2.4.15.tar.gz
    tar -zxf redis-2.4.15.tar.gz
    mv redis-2.4.15 redis
    cd redis
    make

## Running tests

    git clone git://github.com/jodosha/redis-store.git
	cd redis-store/redis-store
	gem install bundler
	ruby ci/run.rb

If you are on **Snow Leopard** you have to run `env ARCHFLAGS="-arch x86_64" ruby ci/run.rb`

## Configuring Redis Store

### Changing storage strategies

Several months ago Matt Huggins (https://github.com/mhuggins) introduced a change to the redis-store gem to decouple how it marshals data into the redis database. Normally, data is marshalled using Ruby's Marshal class. This strategy is fine when access to the data is only given to Ruby-based application but not for any other kind of application unless it has implemented its own Ruby Marshal parser. With this change, a more universal marshal strategy can be applied like JSON. There are caveats to using different strategies such as JSON not serializing Ruby objects properly so make sure that you are using the strategy that fits your needs. 

Example usage:

Each of the following will create a new redis store object that uses the new Marshal adapter
    Redis::Store.new
    Redis::Store.new(:adapter => :marshal)  # symbol shorthand
    Redis::Store.new(:adapter => Redis::Store::Strategy::Marshal)  # actual class
    Redis::Store.new(:adapter => "Redis::Store::Strategy::Marshal")  # string name representing class

Each of the following will create a new redis store object that uses the new Json adapter

    Redis::Store.new(:adapter => :json)   # symbol shorthand
    Redis::Store.new(:adapter => Redis::Store::Strategy::Json)  # actual class
    Redis::Store.new(:adapter => "Redis::Store::Strategy::Json")  # string name representing class

Creating a strategy:

A new strategy simply has to be able to perform two methods: _dump and _load. 

    module RedisYamlAdapter
      def self._dump(object)
        ::YAML.dump(object)
      end

      def self._load(string)
        ::YAML.load(string)
      end
    end

    Redis::Store.new(:adapter => RedisYamlAdapter)

## Copyright

(c) 2009 - 2011 Luca Guidi - [http://lucaguidi.com](http://lucaguidi.com), released under the MIT license
