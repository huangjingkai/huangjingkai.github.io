---
title: My zfs note
tags:
  - zfs
  - ubuntu
---

This is a set of notes for common ZFS command and operations.

A good set of ZFS reference can be found below:
[ZFS admin intro](https://pthree.org/2012/12/04/zfs-administration-part-i-vdevs/)


# Create a ZFS mount

## List all disks

List all paritions:

{% highlight bash %}
lsblk
{% endhighlight %}

Show details of the disks:

{% highlight bash %}
sudo lshw -class disk
{% endhighlight %}

## Create a mirrored pool
The follow would create a mirror (think raid 0) across two devices.

{% highlight bash %}
sudo zpool create tank mirror sdd sde
{% endhighlight %}

# Create a dataset on top of the pool

Instead of directly using the created pool, it is usual to build a
couple of datasets on top of the pool. To create a dataset,

{% highlight bash %}
zfs create tank/test
zfs set compression=lz4 tank/log
zfs set mountpoint=/mnt/test tank/test
zfs mount tank/test
{% endhighlight %}

We also enable the compression of the dataset via set compression.


# Create a NFS share on top of the created dataset

Setup nfs under Ubuntu:

{% highlight bash %}
sudo aptitude install -R nfs-kernel-server
echo '/mnt localhost(ro)' >> /etc/exports
sudo /etc/init.d/nfs-kernel-server start
{% endhighlight %}


Share nfs:

{% highlight bash %}
zfs set sharenfs="rw=@10.80.86.0/24" pool/srv
zfs set sharenfs="on" pool/srv
{% endhighlight %}


# Scrub the dataset regularly

Scrubing check the data integrity:
{% highlight bash %}
zpool scrub tank
{% endhighlight %}

Consider adding above to a cron-job.  A set of script can be found here:
[ZFS scrub and status scripts](http://lawrit.lawr.ucdavis.edu/itdocs/it/how-to/automated-alerts-and-scrubbing-for-zfs-pools)
