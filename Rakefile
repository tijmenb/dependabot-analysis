require 'octokit'
require 'http'

task :run do
  octokit = Octokit::Client.new(access_token: ENV['GITHUB_TOKEN'], auto_paginate: true)

  apps = JSON.parse(HTTP.get('https://docs.publishing.service.gov.uk/apps.json'))

  data = []

  apps.each do |app|
    puts "Fetching #{app}"

    repo = app["links"]["repo_url"].gsub("https://github.com/", "")

    begin
      pull_requests = octokit.pull_requests(
        repo,
        state: 'closed',
      )
    rescue Octokit::NotFound => e
      puts e.inspect
      next
    end

    pull_requests.each do |pr|
      print "."
      next unless pr.user.login == "dependabot[bot]"
      data << [app["app_name"], pr.title, pr.created_at].join(", ")
    end
  end

  File.write("data.csv", data.join(","))
end

task :parse do
  data = []

  File.read("data.csv").lines.each do |ll|
    app_name, pr_title, created_at = ll.split(",")
    gem_name = pr_title.gsub("[Security] ", "").gsub("chore(dependencies): ", "").gsub("build: ", "").split(" ")[1].strip
    data << [app_name, gem_name, Date.parse(date)].join("\t")
  end

  File.write("output.csv", data.jon("/n"))
end
