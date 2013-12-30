# Thor Vcr

This library provides a way inject vcr in front of any thor cli task.  This is useful if you have specs that shell out and call thor task which make network calls and want the response to be mocked out via vcr.

## Installation

Add this line to your application's Gemfile:

    gem 'thor-vcr'

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install thor-vcr

## Usage

In area of the code where dependencies are required, add this line:

    require 'thor/vcr' if ENV['VCR'] == '1'

In spec_helper.rb, add these lines:

```ruby
# Running specs by default will use VCR.
# Can disable via: VCR=0 rake or clean out vcr fixtures via rake clean:vcr.
# Using a ENV VCR flag so that both ruby and cli specs always either use
# vcr or do not use vcr.  It's confusing to only have only ruby use vcr
# and cli specs not using vcr and vice versa.
ENV['VCR'] ? ENV['VCR'] : ENV['VCR'] = '1'

RSpec.configure do |c|
  c.include Helpers
  c.around(:each) do |example|
    VCR.use_cassette(example.metadata[:full_description], :serialize_with => :json) do
      example.run
    end if ENV['VCR'] == '1'
  end
end
```