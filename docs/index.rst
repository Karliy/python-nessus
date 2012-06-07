.. python-nessus documentation master file, created by
   sphinx-quickstart on Sun May  8 00:29:17 2011.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to python-nessus
========================

This library makes it easy to use the Nessus vulnerability scanner from python. With this you can create policies, schedule scans, list reports and take full advantage of the Nessus XMLrpc interface.

Here is how we establish a connection to the server::

    from nessus import NessusConnection
    conn = NessusConnection('username', 'password','127.0.0.1')

After initialising the connection we can now create a simple policy::

    from nessus import PolicyParameters
    params = PolicyParameters()
    params.ssh_credentials('ahall', 'temp123')
    policy = conn.create_policy('policy name', params)

The params are optional but here we want to demonstrate how easy it is to add credentials.

Now we can list the policies and find the one we created::

    policies = conn.list_policies()
    for policy_back in policies:
        if policy_back.id == policy.id:
            print("Found it")
            break

Now if we want to delete the policy we can do it like this::

    conn.delete_policy(policy.id, policy.name)

Now we're ready to schedule a scan that will scan target 192.168.1.0/24 using the policy that we created (given we didn't delete it!)::

    scan = conn.create_scan(policy.id, "new scan", ["192.168.1.0/24"])

Now we can lists the reports and check if our scan is complete::

    for report in conn.list_reports():
        if report.name == scan.uuid and report.status == report.STATUS_COMPLETE:
            break

NOTE: The scan uuid becomes the report name, that's the way Nessus works.

Now lets download the report into /tmp/myreport.nessus::

    with open('/tmp/myreport.nessus', 'wb'):
        conn.download_report(scan.uuid, np)
