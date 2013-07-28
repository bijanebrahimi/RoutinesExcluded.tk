keeping entire server configurations in one git repository

it's a common situation when you lost a configuration file. it happened to me a lot as a matter of fact :) my first solution was to keep configurations for each service in a different git repository. soon i realized that it would be time-consuming to keep all the repositories up to date or pulling them all when migrating to a new server or ... it seemed that one huge git repository is the best approach to keep all the necessary configurations into it. to do that, we can create the git repository in the `/` directory and create a white-list to include only configuration files we are interested in them.

<!-- Content Breaker -->

> Note: you can find the final code in [My Local Debian-Linux configuration](https://bitbucket.org/bijanebrahimi/locallinux-debian-wheezy/overview)

## White-List using .gitignore file

unfortunately git doesn't support white list but the `.gitignore` is sufficient enought to do the same job. to do that we first have to create the repository first.

    # cd /
    # git init

now that we created the initial repository, we need to create the `.gitignore` file. to create a white-list, we need to ignore everything and then start including the configuration directories. since all we can do in `.gitignore` is to exclude things, we have to use the `!` mark to exclude them from being excluded. simple, right?

    # cat > .gitignore << EOF
    *
    !/.gitignore
    EOF

we ignored evething by the `*` in the first line, but to actually let the `.gitignore` do the white-list job, we have to include it. so by excluding it from being excluded, we created out first white-list file. note that we add `/.gitignore` because we just want the `.gitignore` file which is in the root directory to be include not any other.

## includeing sub-directoris

adding subdirectories are a little bit tricky. imagine we want to include the `grub.cfg` file in our git repository. we can not simply add `!/boot/grub/grub.cfg` to our gitignore file. that won't work. why? because the `/boot` directory is ignored by the first line in the `.gitignore` file. all we have to do is to first include the boot directory and it's subdirectories and then include the grub configuration file.

    # cat > .gitignore << EOF
    *
    !/.gitignore
    
    !boot
    !boot/grub
    !boot/grub/grub.cfg
    EOF

Note: *we can put another `.gitignore` in each configuration directory and include it in the main `/.gitignore` file. that way, we avoid getting our white list too messy and complicated.*

that's it. now we can add the files and commit the changes. the next best thing would be to push the changes to a remote repository. i used [bitbucket](https://bitbucket.org) since i can have private repositories for configurations i don't want it to be publically available (containing email addresses or even raw passwords). **you will need to change the git repository remote address to yours**.

    # git remote add bitbucket https://bijanebrahimi@bitbucket.org/bijanebrahimi/locallinux-debian-wheezy.git
    # git add .
    # git commit -m "added grub configuration"
    # git push bitbucket master
   
that's it :D but there's still one problem. what if i actually migrate to a new server? git doesn't keep track of file's permissions and their owners. we need to preserve them when cloning a new fork. to do that, i found `git-cache-meta` script a big help. what it does is simple but important. it gets the list of files from git repository, gets their owner, groups, permission and creation times and preserves them in `.git_cache_meta` file. you can download it from [here](https://gist.github.com/andris9/1978266) and to install it:

    # cat > /usr/bin/loca/git-cache-meta << EOF
    #!/bin/sh -e
    #git-cache-meta -- simple file meta data caching and applying.
    #Simpler than etckeeper, metastore, setgitperms, etc.
    #from http://www.kerneltrap.org/mailarchive/git/2009/1/9/4654694
    #modified by n1k
    # - save all files metadata not only from other users
    # - save numeric uid and gid
     
    # 2012-03-05 - added filetime, andris9
     
    : ${GIT_CACHE_META_FILE=.git_cache_meta}
    case $@ in
        --store|--stdout)
        case $1 in --store) exec > $GIT_CACHE_META_FILE; esac
        find $(git ls-files)\
            \( -printf 'chown %U %p\n' \) \
            \( -printf 'chgrp %G %p\n' \) \
            \( -printf 'touch -c -d "%AY-%Am-%Ad %AH:%AM:%AS" %p\n' \) \
            \( -printf 'chmod %#m %p\n' \) ;;
        --apply) sh -e $GIT_CACHE_META_FILE;;
        *) 1>&2 echo "Usage: $0 --store|--stdout|--apply"; exit 1;;
    esac
    # chmod +x /usr/bin/loca/git-cache-meta

and then all we have to do is to create `.git_cache_meta` file and then update it whenever a new file is added or an already existing one meta-data changes. to do that simply call it by `--restore` argument.

    # cd /
    # git-cache-meta --restore

to apply the permission, you can call it by using `--apply` argument. but first remember that you have to include the `!/.git_cache_meta` to your gitignore file, commit and push the changes to remote repository. then in the new server we have to pull the git repository and apply the `git-cache-meta` if necessary. for example, imagine we migrated to a new server and we want our configuration to be applied there as well (please rememeber to change the repository url to your own otherwise you end up messing with your configuration with mine):

    # cd /
    # git init
    # git remote add bitbucket https://bijanebrahimi@bitbucket.org/bijanebrahimi/locallinux-debian-wheezy.git
    # git pull bitbucket master
    # git-cache-meta --apply

* you can find the final code in [my gitbucket repository](https://bitbucket.org/bijanebrahimi/locallinux-debian-wheezy/overview)
* you can also download the markdown source of this blog entry from [here](keeping-entire-server-configurations-in-one-git-repository.md). GFDL licensed by Bijan Ebrahimi
