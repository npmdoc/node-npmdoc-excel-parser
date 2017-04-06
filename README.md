# api documentation for  [excel-parser (v0.2.2)](https://github.com/vxtindia/excel-parser)  [![npm package](https://img.shields.io/npm/v/npmdoc-excel-parser.svg?style=flat-square)](https://www.npmjs.org/package/npmdoc-excel-parser) [![travis-ci.org build-status](https://api.travis-ci.org/npmdoc/node-npmdoc-excel-parser.svg)](https://travis-ci.org/npmdoc/node-npmdoc-excel-parser)
#### node.js excel parser. Supports xlsx, xls

[![NPM](https://nodei.co/npm/excel-parser.png?downloads=true)](https://www.npmjs.com/package/excel-parser)

[![apidoc](https://npmdoc.github.io/node-npmdoc-excel-parser/build/screenCapture.buildNpmdoc.browser._2Fhome_2Ftravis_2Fbuild_2Fnpmdoc_2Fnode-npmdoc-excel-parser_2Ftmp_2Fbuild_2Fapidoc.html.png)](https://npmdoc.github.io/node-npmdoc-excel-parser/build/apidoc.html)

![npmPackageListing](https://npmdoc.github.io/node-npmdoc-excel-parser/build/screenCapture.npmPackageListing.svg)

![npmPackageDependencyTree](https://npmdoc.github.io/node-npmdoc-excel-parser/build/screenCapture.npmPackageDependencyTree.svg)



# package.json

```json

{
    "author": {
        "name": "Shekhar R. Thawali",
        "email": "shekhar.thawali@vxtindia.com"
    },
    "bugs": {
        "url": "https://github.com/vxtindia/excel-parser/issues"
    },
    "dependencies": {
        "async": "~0.2.7",
        "temp": "~0.5.0",
        "underscore": "~1.4.4"
    },
    "description": "node.js excel parser. Supports xlsx, xls",
    "devDependencies": {
        "grunt": "~0.4.1",
        "grunt-contrib-internal": "~0.4.3",
        "grunt-contrib-jshint": "~0.3.0",
        "grunt-contrib-nodeunit": "~0.1.2",
        "grunt-contrib-watch": "~0.3.1"
    },
    "directories": {},
    "dist": {
        "shasum": "2c2775fe440c1584f9dfdbc8220ed8cf29980d52",
        "tarball": "https://registry.npmjs.org/excel-parser/-/excel-parser-0.2.2.tgz"
    },
    "engines": {
        "node": "~0.8.2"
    },
    "homepage": "https://github.com/vxtindia/excel-parser",
    "keywords": [
        "excel",
        "spreadsheet",
        "xls",
        "xlsx"
    ],
    "licenses": [
        {
            "type": "MIT",
            "url": "https://github.com/vxtindia/datlas/blob/master/LICENSE-MIT"
        }
    ],
    "main": "excelParser.js",
    "maintainers": [
        {
            "name": "sthawali",
            "email": "shekhar.thawali@vxtindia.com"
        }
    ],
    "name": "excel-parser",
    "optionalDependencies": {},
    "readme": "ERROR: No README data found!",
    "repository": {
        "type": "git",
        "url": "git://github.com/vxtindia/excel-parser.git"
    },
    "scripts": {
        "test": "grunt nodeunit"
    },
    "version": "0.2.2",
    "warnings": [
        {
            "code": "ENOTSUP",
            "required": {
                "node": "~0.8.2"
            },
            "pkgid": "excel-parser@0.2.2"
        },
        {
            "code": "ENOTSUP",
            "required": {
                "node": "~0.8.2"
            },
            "pkgid": "excel-parser@0.2.2"
        }
    ]
}
```



# <a name="apidoc.tableOfContents"></a>[table of contents](#apidoc.tableOfContents)

#### [module excel-parser](#apidoc.module.excel-parser)
1.  [function <span class="apidocSignatureSpan">excel-parser.</span>parse (options, cb)](#apidoc.element.excel-parser.parse)
1.  [function <span class="apidocSignatureSpan">excel-parser.</span>worksheets (options, cb)](#apidoc.element.excel-parser.worksheets)



# <a name="apidoc.module.excel-parser"></a>[module excel-parser](#apidoc.module.excel-parser)

#### <a name="apidoc.element.excel-parser.parse"></a>[function <span class="apidocSignatureSpan">excel-parser.</span>parse (options, cb)](#apidoc.element.excel-parser.parse)
- description and source-code
```javascript
parse = function (options, cb) {
  var args = [], _this = this;
  if(!cb) throw new Error("worksheets will required two arguments. worksheets(options, callback)");
  else if(!options || typeof(options) !== 'object') return cb("Invalid arguments.");
  else if(!options.inFile) return cb("File is missing in arguments");
  fs.exists(options.inFile, function(exists) {
    if(!exists) return cb("File not found");
    args = ['-x', '"'+path.relative(__dirname, options.inFile)+'"'];
    if(!options.worksheet) {
      var records = [];
      _this.worksheets(options, function(err, worksheets) {
        if(err) return cb(err);
        async.series(_.map(worksheets, function(worksheet) {
          return function(rcb) {
            args.push('-n', worksheet.id);
            utils.pickRecords(args, options, function(err, parsed) {
              if(err) return cb(err);
              records.push(parsed);
              rcb(null);
            });
          }
        }), function(err, parsed) {
          if(err) return cb(err);
          cb(null, parsed);
        });
      });
    } else {
      if(typeof(options.worksheet) === 'number') {
        args.push('-n', options.worksheet);
      } else if(typeof(options.worksheet) === 'string') {
        args.push('-w', '"'+ options.worksheet.trim() + '"');
      } else {
        return callback(new Error('"worksheet" should be string or intiger'));
      }
      utils.pickRecords(args, options, function(err, parsed) {
        if(err) return cb(err);
        cb(null, parsed);
      });
    }
  });
}
```
- example usage
```shell
...
** 'loose' is for the case insensetive and match relatively.

__Example__

'''javascript
var excelParser = require('excel-parser');

excelParser.parse({
inFile: 'my_file.in',
worksheet: 1,
skipEmpty: true,
searchFor: {
  term: ['my serach term'],
  type: 'loose'
}
...
```

#### <a name="apidoc.element.excel-parser.worksheets"></a>[function <span class="apidocSignatureSpan">excel-parser.</span>worksheets (options, cb)](#apidoc.element.excel-parser.worksheets)
- description and source-code
```javascript
worksheets = function (options, cb) {
  var worksheets, cb = cb && typeof(cb) === "function" ? cb : null, args = [];
  if(!cb) throw new Error("worksheets will required two arguments. worksheets(options, callback)")
  else if(!options || typeof(options) !== 'object') return cb("Invalid arguments.");
  else if(!options.inFile) return cb("File is missing in arguments");

  fs.exists(options.inFile, function(exists) {
    if(!exists) return cb("File not found");
    args = ['-x', '"'+path.relative(__dirname, options.inFile)+'"', '-W'];
    utils.execute(args, function(err, stdout) {
      if(err) return cb(err);
      worksheets = _.compact(stdout.split(/\n/));
      if(worksheets) return cb(null, JSON.parse(worksheets));
      else return cb(new Error("Not found any worksheet in given spreadsheet"));
    });
  });
}
```
- example usage
```shell
...

* inFile - Filepath of the source speadsheet

__Example__

'''js
var excelParser = require('excel-parser');
excelParser.worksheets({
  inFile: 'my_file.in'
}, function(err, worksheets){
  if(err) console.error(err);
  consol.log(worksheets);
});
'''
__Sample output__
...
```



# misc
- this document was created with [utility2](https://github.com/kaizhu256/node-utility2)
