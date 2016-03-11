GWT Spring brings Spring's proven, simple dependency injection (DI) principles to your GWT project. It allows for transparent, automatic access to Spring beans as top level services from GWT Client code without extra servlets or the boilerplate RPC wrappers that are frequently needed for effective Spring/GWT integration. Most importantly, GWT Spring is implemented with a lightweight vanilla Java servlet not requiring any additional dependencies like Spring MVC to function.

Currently Java 5+, GWT 1.6.4+, and Spring 2.5+ are officially supported though many features may work with other versions.

See [SimpleRPCExample](SimpleRPCExample.md) and the related example code for an introduction to how spring4gwt works.

**Current Status**

The current version is an initial build supporting transparent service lookup using GWT.create(). Since a final version of Google Gin is now available providing very rich DI functionality in GWT there are no plans to continue spring4gwt beyond the current RPC bridge. Using spring4gwt together with Gin on the client and Spring on the server is a great way to have dependency injection throughout your application.