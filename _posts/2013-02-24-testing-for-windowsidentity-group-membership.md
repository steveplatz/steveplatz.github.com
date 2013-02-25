---
layout: default
title: Testing for WindowsIdentity Group Membership
tags: activedirectory windowsidentity membership  
description: How to test for WindowsIdentity Group Membership
keywords: activedirectory, ad, windowsidentity, membership, .net, c#
---

# Testing for WindowsIdentity Group Membership
 
The following code sample outlines a method for determining the group membership of a Windows user using the `WindowsIdentity` class. This method attempts to be as quick as possible by only comparing the Group SID and not resolving the details for each group a user belongs to.

{% highlight c# linenos %}
public static class WindowsIdentityExtensions
{
    public static bool IsInGroup(this WindowsIdentity value, string groupName)
    {
        if (value == null)
            return false;
        if (string.IsNullOrWhiteSpace(groupName))
            return false;

        using (var ctx = new PrincipalContext(ContextType.Domain))
        {
            using (var group = GroupPrincipal.FindByIdentity(ctx, IdentityType.Name, groupName))
            {
                return group != null &&
                    value.Groups.Select(g => g.Value).Contains(group.Sid.Value);
            }
        }
    }
}

// Usage
WindowsIdentity identity = WindowsIdentity.GetCurrent();
identity.IsInGroup("GroupName"); // Returns true or false
{% endhighlight %}