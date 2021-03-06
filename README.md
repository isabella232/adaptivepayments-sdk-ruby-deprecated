# PayPal Ruby Adaptive Payments SDK

## NOTE: THIS REPOSITORY IS DEPRECATED. CODE PUBLISHED FOR HISTORICAL PURPOSES

#### Adaptive Payments moving to limited release

> **Important**: Adaptive Payments is now a limited release product. It is restricted to select partners for approved use cases and should not be used for new integrations without guidance from PayPal.

## Support

> Please contact [PayPal Technical Support](https://developer.paypal.com/support/) for any live or account issues.

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'paypal-sdk-adaptivepayments'
```

And then execute:

```sh
$ bundle
```

Or install it yourself as:

```sh
$ gem install paypal-sdk-adaptivepayments
```

## Configuration

For Rails application:

```sh
rails g paypal:sdk:install
```

For other ruby application, create a configuration file(`config/paypal.yml`):

```yaml
development: &default
  username: USERNAME
  password: PASSWORD
  signature: SIGNATURE
  app_id: APP-80W284485P519543T
  http_timeout: 30
  mode: sandbox
  sandbox_email_address: Platform.sdk.seller@gmail.com
  # # with certificate
  # cert_path: "config/cert_key.pem"
  # # with token authentication
  # token: TOKEN_AUTHENTICATION
  # token_secret: TOKEN_SECRET
  # # with Proxy
  # http_proxy: http://proxy-ipaddress:3129/
  # # with device ip address
  # device_ipaddress: "127.0.0.1"
test:
  <<: *default
production:
  mode: live
  username: USERNAME
  password: PASSWORD
  signature: SIGNATURE
  app_id: APP_ID
```

Load Configurations from specified file:

```ruby
PayPal::SDK.load('config/paypal.yml',  ENV['RACK_ENV'] || 'development')
```

Or without configuration file:

```ruby
PayPal::SDK.configure(
  :mode      => "sandbox",  # Set "live" for production
  :app_id    => "APP-80W284485P519543T",
  :username  => "Replace with API username",
  :password  => "Replace with API password",
  :signature => "Replace with API signature" )
```

## Example

```ruby
require 'paypal-sdk-adaptivepayments'
PayPal::SDK.configure(
  :mode      => "sandbox",  # Set "live" for production
  :app_id    => "APP-80W284485P519543T",
  :username  => "Replace with API username",
  :password  => "Replace with API password",
  :signature => "Replace with API signature" )

@api = PayPal::SDK::AdaptivePayments.new

# Build request object
@pay = @api.build_pay({
  :actionType => "PAY",
  :cancelUrl => "http://localhost:3000/samples/adaptive_payments/pay",
  :currencyCode => "USD",
  :feesPayer => "SENDER",
  :ipnNotificationUrl => "http://localhost:3000/samples/adaptive_payments/ipn_notify",
  :receiverList => {
    :receiver => [{
      :amount => 1.0,
      :email => "platfo_1255612361_per@gmail.com" }] },
  :returnUrl => "http://localhost:3000/samples/adaptive_payments/pay" })

# Make API call & get response
@response = @api.pay(@pay)

# Access response
if @response.success? && @response.payment_exec_status != "ERROR"
  @response.payKey
  @api.payment_url(@response)  # Url to complete payment
else
  @response.error[0].message
end
```

## Samples App

Add following line in rails `Gemfile`:

```ruby
gem 'paypal-sdk-adaptivepayments'
gem 'adaptive_payments_samples', :git => "https://github.com/paypal/adaptivepayments-sdk-ruby.git", :group => :development
```

Configure routes(`config/routes.rb`):

```ruby
mount AdaptivePaymentsSamples::Engine => "/samples" if Rails.env.development?
```

To get default paypal configuration execute:

```sh
rails g paypal:sdk:install
```

Run `rails server` and check the samples.
