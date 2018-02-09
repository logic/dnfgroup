`dnfgroup` adds a package provider to puppet so you can manage DNF groups in a
familar way:

```puppet
package {
    'workstation-product-environment':
        ensure   => 'installed',
        provider => 'dnfgroup';
}
```

On Fedora, that will ensure that the "Fedora Workstation" environment group
(group ID "workstation-product-environment") is installed, which will install
any subgroups and individual packages.

This provider doesn't make any distinctions between different types of groups
(groups, environment groups, language groups), as there appear to be no
technical reasons to differentiate at the puppet level.

You can accomplish something very similar to what this provider does with the
default dnf provider by using the group-id, prefixed by `@`, as the package
name. However, the dnf provider won't register the group as installed, because
it doesn't know about that particular magic syntax, so it will attempt to
reinstall it with every run (even if that doesn't sound too bad, if you `ensure
=> 'absent'` a package that happens to be in that group, it will get installed
and then immediately removed every run). This provider also allows you to refer
to groups by their longer name (which are added as aliases), rather than by ID.

This is an unusual package provider, in that it doesn't support versions (since
there's no versioning of DNF groups), and as a result, there's no support for
upgrading (via `ensure => 'latest'`), because there's no easy way to determine
if an upgrade is needed without actually testing the transaction, which is a
very expensive check for something of fairly low utility (assuming you have an
out-of-band mechanism for handling package upgrades).
