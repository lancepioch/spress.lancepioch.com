---
layout: post
categories: [example]
tags: [table]
class: "page"
---

![Example of Process List](http://i.imgur.com/5oHMeMz.png)

## The Catalyst

[Process Explorer v16.0](http://technet.microsoft.com/en-us/sysinternals/bb896653) has just been released and includes a very neat function. It allows you to select one option and it automatically checks each of your processes online using VirusTotal's service. While this certainly won't catch any of the brand new viruses, it can very quickly tell you with a high accuracy if you are running any questionable processes.

The new Windows 8 task manager is very spiffy and not only looks nice, but functions better too. I really don't want to replace it just to have this one useful function. And that's exactly the perfect reason for a do it yourself project here!

## Retrieving Processes

The first thing to start off with is the best way to reliably get the processes list off Windows. For Linux you'll want to see this [StackOverflow discussion](http://stackoverflow.com/questions/2703640/process-list-on-linux-via-python).

After a bit of googling, it seems that a really good way is to use [WMIC](http://technet.microsoft.com/en-us/library/bb742610.aspx) and simply pipe it in. The command would be: `wmic process`. This will give you a huge list with every single detail that you'd find in Task Manager all running through your command line. Not exactly what we want. We only really care about two things, the name of the process and its physical file location. To do that, we enhance what we already had: `wmic process get description,executablepath`. Bam, a list of just minimally what we want.

Now to get that into python, we'll need to make usage of subprocess.Popen. The first argument being our command, the next setting `shell=True` and teh last having our `stdout=subprocess.PIPE` so we can pipe the output. The stdout of the subprocess returns a File object. It's as simple as using readline or a for loop.

Since the output is spaced out from each other, rather than tabs, we'll have to use the first line to determine the position of all the processes' paths. Use readline to get the first line of the output and then use that string's index method to find "ExecutablePath". Now it's as simple as looping through the rest of the stdout File and adding the results to a couple lists.

We're going to also make sure that the program's path is greater than one in length to prevent system processes from getting caught in there (that have no actual file location). Also to make sure there are not any duplicate file paths, it's not really necessary to submit the same exact file hash multiple times. Finally make sure to strip off all that extra empty spaces off the strings.

### Code:
    import subprocess
    
    def getProcesses():
    	cmd = 'wmic process get description,executablepath'
    	proc = subprocess.Popen(cmd, shell=True, stdout=subprocess.PIPE)
    
    	checkline = proc.stdout.readline()
    	index = checkline.index("ExecutablePath")
    
    	names = []
    	paths = []
    
    	for line in proc.stdout:
    		appname = line[0:index].strip()
    		program = line.strip()[index:]
    		if len(program) > 0 and program not in paths:
    			paths.append(program)
    			names.append(appname)
    
    	return names, paths

## Generating File Checksums

VirusTotal says that they accept md5, sha1, and sha256 hashes of files. I chose to use sha256, but you can just as easily change it to md5 or sha1 because we're going to be using hashlib, and VirusTotal determines the hash type from its length.

Best way to get a file's hash? Well for all I know, you could be running a huge game or some other process. We need a reliable way to parse through a file's contents and hash it. A little bit of searching turns up this [StackOverflow answer](http://stackoverflow.com/questions/3431825/python-generating-a-md5-checksum-of-a-file). The only difference is that we want the hexdigest instead of the actual binary.

### Code:
    def hashFile(afile, hasher, blocksize=65536):
        buf = afile.read(blocksize)
        while len(buf) > 0:
            hasher.update(buf)
            buf = afile.read(blocksize)
        return hasher.hexdigest()

This function takes a File (object) handle, a hashlib hashing function, and an optional blocksize for reading the file.

Here's a small helper function to demonstrate this.

### Code:
    import hashlib
    
    def sha(filepath):
    	return hashFile(open(filepath, 'rb'), hashlib.sha256())

## Hashing in Parallel

I'm honestly going to just direct you to this great article, "[Parallelism in one line](https://medium.com/building-things-on-the-internet/40e9b2b36148)" and implement the very simple solution they have. They have explained it very well.

### Code:
    from multiprocessing.dummy import Pool as ThreadPool
    
    def hashParallel(paths, threads=2):
    	pool = ThreadPool(threads)
    	results = pool.map(sha, paths)
    	pool.close()
    	pool.join()
    	return results

Typically the number of threads your computer has is the number of cores it has. However in my case, I have an i5 2500k which has four cores and hyperthreading, which allows each core to simutaneously run two threads at once. Therefore I use 8 threads for maximum efficiency, getting the hashes in less than a couple of seconds.

## VirusTotal API

In three easy steps, you can have access to your very own, limited api key. Yes, it's limited to 4 requests every minute, so we'll be joining all the hashes together in one single request.

1. Register an account
2. Confirm your email
3. Get your API key

You can find out most of this information on their website. We need a comma separated string containing the hashes, and the apikey to post to their api url which is `https://www.virustotal.com/vtapi/v2/file/report`. We'll be using urllib2 to send the request and urllib to encode the parameters.

### Code:
    import urllib, urllib2
    
    def filesReport(apikey, hashlist):
    	hashes = ', '.join(hashlist)
    	url = "https://www.virustotal.com/vtapi/v2/file/report"
    	parameters = {"resource": hashes, "apikey": apikey}
    	data = urllib.urlencode(parameters)
    	req = urllib2.Request(url, data)
    	response = urllib2.urlopen(req)
    
    	return response.read()

## Replicating Behavior

From that request, we get some very nice json. Using the built in json module, we can use the `json.loads` function to translate the request (string) into an object. There all we are looking for is the `positives` and the `total` keys.

### Code:
    import json
    
    if __name__ == "__main__":
    	print "Getting process list..."
    	processes = getProcesses()
    	print "Found " + str(len(processes[1])) + " non system, unique processes running."
    
    	print "Started calculating file hashes..."
    	results = hashParallel(processes[1], 8)
    	print "Finished calculating sha256 hashes."
    
    	print "Retrieving results from virustotal.com..."
    	apikey = "Copy and paste your API key here!"
    	output = filesReport(apikey, results)
    	files = json.loads(output)
    	print "Successfully acquired results:\n"
    
    	for f, n in zip(files, processes[0]):
    		print n + " = " + str(f["positives"]) + " / " + str(f["total"])