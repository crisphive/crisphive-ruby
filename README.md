# Crisphive Ruby

The official Ruby SDK for the
[Crisphive API](https://docs.crisphive.com/).

Typed access to the public `/v1` API — customers, bookings, catalog, team and
fleet.

## Requirements

Ruby 2.7+.

## Installation

```sh
gem install crisphive
```

Or add to your `Gemfile`:

```ruby
gem "crisphive"
```

## Authentication

Every request is authenticated with a secret API key sent as a bearer token.
Create keys from your Crisphive business dashboard. **The key prefix selects the
data environment:**

- `chsk_live_…` → live (production) data
- `chsk_test_…` → sandbox (isolated test) data

Load keys from the environment — never commit them.

## Usage

```ruby
require "crisphive"

Crisphive.configure do |config|
  config.access_token = ENV["CRISPHIVE_API_KEY"] # SDK adds the "Bearer " prefix
end

customers = Crisphive::CustomerApi.new

# List customers.
res = customers.list_customers(limit: 20)
(res.data.customers || []).each do |c|
  puts "#{c.id} #{c.full_name}"
end

# Create a customer.
begin
  created = customers.create_customer(
    Crisphive::CustomerCreateRequest.new(full_name: "Ada Lovelace", email: "ada@example.com")
  )
  puts "created #{created.data.customer_id}"
rescue Crisphive::ApiError => e
  puts "error #{e.code} #{e.response_body}"
end
```

## Pagination

List methods accept `page:` / `limit:` and return a `meta` object (`total`,
`count`, `per_page`, `current_page`, `total_pages`).

## Idempotency

`create_*` calls (customers, bookings) accept an `Idempotency-Key` header so
retries never create a duplicate.

## Errors

Non-2xx responses raise `Crisphive::ApiError`; inspect `e.code` and
`e.response_body` for the Crisphive error code.

## Documentation

- Docs: https://docs.crisphive.com
- API reference: https://docs.crisphive.com/technical-reference
- Webhooks: https://docs.crisphive.com/webhook

## License

[MIT](LICENSE)
