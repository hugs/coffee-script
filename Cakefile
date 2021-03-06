fs: require 'fs'
coffee: require 'coffee-script'

# Run a CoffeeScript through our node/coffee interpreter.
run: (args) ->
  proc: process.createChildProcess 'bin/coffee', args
  proc.addListener 'error', (err) -> if err then puts err


task 'install', 'install CoffeeScript into /usr/local', ->
  exec([
    'mkdir -p /usr/local/lib/coffee-script'
    'cp -rf bin lib LICENSE README package.json src vendor /usr/local/lib/coffee-script'
    'ln -sf /usr/local/lib/coffee-script/lib/bin/coffee /usr/local/bin/coffee'
    'ln -sf /usr/local/lib/coffee-script/lib/bin/cake /usr/local/bin/cake'
  ].join(' && '))


task 'build', 'build the CoffeeScript language from source', ->
  fs.readdir 'src', (err, files) ->
    files: 'src/' + file for file in files when file.match(/\.coffee$/)
    run ['-o', 'lib'].concat(files)


task 'build:parser', 'rebuild the Jison parser (run build first)', ->
  parser: require('grammar').parser
  js: parser.generate()
  parser_path: 'lib/parser.js'
  fs.writeFile parser_path, js


task 'build:ultraviolet', 'build and install the Ultraviolet syntax highlighter', ->
  exec 'plist2syntax extras/CoffeeScript.tmbundle/Syntaxes/CoffeeScript.tmLanguage', (err) ->
    exec 'sudo mv coffeescript.yaml /usr/local/lib/ruby/gems/1.8/gems/ultraviolet-0.10.2/syntax/coffeescript.syntax'


task 'build:underscore', 'rebuild the Underscore.coffee documentation page', ->
  exec 'uv -s coffeescript -t idle -h examples/underscore.coffee > documentation/underscore.html'


task 'build:browser', 'rebuild the merged script for inclusion in the browser', ->
  exec 'rake browser'


task 'doc', 'watch and continually rebuild the documentation', ->
  exec 'rake doc'


task 'test', 'run the CoffeeScript language test suite', ->
  process.mixin require 'assert'
  test_count: 0
  start_time: new Date()
  [original_ok, original_throws]: [ok, throws]
  process.mixin {
    ok:     (args...) -> test_count += 1; original_ok(args...)
    throws: (args...) -> test_count += 1; original_throws(args...)
  }
  process.addListener 'exit', ->
    time: ((new Date() - start_time) / 1000).toFixed(2)
    puts '\033[0;32mpassed ' + test_count + ' tests in ' + time + ' seconds\033[0m'
  fs.readdir 'test', (err, files) ->
    for file in files
      fs.readFile 'test/' + file, (err, source) ->
        js: coffee.compile source
        process.compile js, file