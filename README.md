# passport-cas-yale

Yale CAS authentication strategies redeveloped for Passport.


## Install

    $ npm install passport-cas-yale

#### Configure Strategy
    passport.use(new (require('passport-cas-yale').Strategy)({
      ssoBaseURL: 'https://secure.its.yale.edu/cas',
      serverBaseURL: 'http://localhost:8080'
    }, function(login, done) {
      User.findOne({login: login}, function (err, user) {
        if (err) {
          return done(err);
        }
        if (!user) {
          return done(null, false, {message: 'Unknown user'});
        }
        return done(null, user);
      });
    }));

#### Authenticate Requests

##### passport.js
    passport.use(new(require('passport-cas-yale').Strategy)({
      ssoBaseURL: 'https://secure.its.yale.edu/cas',
      serverBaseURL: 'http://localhost:8080'
    }, function(login, done) {
      User.findOne({
        login: login
      }, function(err, user) {
        if (err) {
          return done(err);
        }
        if (!user) {
          return done(null, false, {
            msg: 'Unknown user'
          });
        }
        return done(null, user);
      });
    }));

For example:

##### app.js
    app.get('/auth/cas', apiController.casLogin);

##### api.js
    exports.casLogin = function(req, res, next) {
      passport.authenticate('cas', function(err, user, info) {
        if (err) {
          return next(err);
        }

        if (!user) {
          req.session.messages = info.message;
          return res.redirect('/');
        }

        req.logIn(user, function(err) {
          if (err) {
            return next(err);
          }

          req.session.messages = '';
          return res.redirect('/');
        });
      })(req, res, next);
    };

### CAS versions

Passport Yale CAS only works with CAS 1.0.

## License

[The MIT License](http://opensource.org/licenses/MIT)
