require 'kindle_highlights'
require 'mail'

Mail.defaults do
  delivery_method :smtp,
    address:              ENV['MAILGUN_SMTP_SERVER'],
    port:                 ENV['MAILGUN_SMTP_PORT'],
    user_name:            ENV['MAILGUN_SMTP_LOGIN'],
    password:             ENV['MAILGUN_SMTP_PASSWORD'],
    to:                   ENV['AMAZON_USERNAME'],
    authentication:       'plain',
    enable_starttls_auto: true
end

class HighlightsFetcher
  PAGE_FETCH_FAILURE_MESSAGE = "undefined method `email=' for nil:NilClass"
  IGNORED_ASINS = [
    "B01LFM17JO", # Buddhism
    "B07RK4MM5X", # A Wedding for the Spitfire Girl
    "B07H97TC7D", # The Girl from the Corner Shop
    "B06XX3QB77", # Hattie's Home
    "B01LZJWFXH", # The Slicing Pie Handbook
    "B0096EFHBI", # Slicing Pie
  ]

  def fetch(amount = 3)
    retries = 0

    begin
      all_highlights.sample(amount).sort.join("<br/><br/>")
    rescue => e
      raise e unless e.message.include?(PAGE_FETCH_FAILURE_MESSAGE)

      retries += 1
      sleep(0.3) # Sleep to give some time between retries
      retry if retries <= 10
    end
  end

  private

  def client
    @client ||= KindleHighlights::Client.new(
      email_address: ENV["AMAZON_USERNAME"],
      password: ENV['AMAZON_PASSWORD']
    )
  end

  def all_highlights
    return @all_highlights if defined?(@highlights)

    @all_highlights = []

    included_books = client.books.reject{ |book| IGNORED_ASINS.include?(book.asin) }
    included_books.each do |book|
      client.highlights_for(book.asin).each do |highlight|
        text = "<p><b>#{book.title} by #{book.author}</b><br/><br/><i>#{highlight.text}</i></p>"
        @all_highlights << text unless highlight.text.nil?
      end
    end

    return @all_highlights
  end
end

task :print do
  puts HighlightsFetcher.new.fetch
end

task :default do
  mail = Mail.new do
    from    'Kindle Highlights <kindle@sandbox.mgsend.net>'
    subject "#{Time.now.strftime("%b %d")} Highlights"
    to ENV['AMAZON_USERNAME']
    html_part do
      content_type 'text/html; charset=UTF-8'

      body HighlightsFetcher.new.fetch
    end
  end

  mail.deliver

  puts "OK, sent email"
end

