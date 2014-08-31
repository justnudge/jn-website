---
layout: post
title: Encrypting data with Chef
tags: [chef, encryption]
---
I know that this may be covered elsewhere, but I struggled to find a single resource that detailed
clearly how to do this.  What I can work out is that there appeared to be a number of ways to do this
but I detail the one that worked for me.

The first step in encrypting your data is to create an encryption key:

    openssl rand -base64 4096 | tr -d '\r\n' > encrypted_data_bag_secret

I placed this encryption key in the root of my Chef repository, but in truth it can be stored anywhere.

The next step is to create the data to encrypt.  This is a json file in a similar format to the following:

    {
        "id": "mysql",
        "username": "jira",
        "password": "password"
    }

The data can be any data that is in a json format.  To encrypt the data and load it into Chef run the following
command:

    knife data bag from file passwords data_bags/mysql.json --secret-file encrypted_data_bag_secret

In the above command the "data_bags/mysql.json" is the json file created above and the encrypted_data_bag_secret
is the encryption key.

Successful encryption can be shown by running the following command:

    knife data bag show passwords mysql

Which should show something similar to the following:

<img src="/assets/chef-databag-encrypted.png" class="img-responsive" alt="Chef Encrypted Databag" />

Obviously encrypted databags aren't that useful if we can't decrypt it, in order to decrypt it the key needs to be
added to the above command:

    knife data bag show --secret-file encrypted_data_bag_secret passwords mysql

And it should output the following:

    id:       mysql
    password: password
    username: jira

Encrypted content can also be viewed via the Chef UI:

<img src="/assets/chef-ui-database-encrypted.png" class="img-responsive" alt="Chef UI Encrypted Databag" />

Lastly, the host itself needs to be able to access the decrypted contents.  This is done by
dropping the encryption key onto the host and then access the data bag as follows:

    mysql_secret = Chef::EncryptedDataBagItem.load_secret("/etc/.encrypt_key")
    mysql_creds = Chef::EncryptedDataBagItem.load("passwords", "mysql", mysql_secret)
    username = mysql_creds["username"]
    password = mysql_creds["password"]
  
    file "/tmp/output.txt" do
        content username + ":" + password
        mode 0600
    end
    
The contents of the file is as follows:

    [root@jboss01 tmp]# cat output.txt 
    jira:password
    
And password encryption with Chef works!