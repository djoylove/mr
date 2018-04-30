#!/usr/bin/env bash
#!/bin/bash
#author daijia

FULL_REMOTE_URL=`git config --get remote.origin.url`
GITLAB_API_ADDRESS="http://git.guazi-corp.com/api/v4"
GITLAB_ADDRESS="http://git.guazi-corp.com/"
GET_PROJECT_URL="/projects/"
PROJECT_NAME=""
MERGE_REQUEST_PATTERN="\"merge_requests\".*merge_requests\""
HTTP_MERGE_REQUEST_PATTERN="http.*.*merge_requests"
AUTHENTICATION_URL="?private_token=$GITLAB_TOKEN"
MERGE_REQUEST_URL=""
SOURCE_BRANCH=""
getProject() {
    if [ -z "$FULL_REMOTE_URL" ]; then
        echo "remote url is empty, pls check your git config"
        exit 1
    fi
    NAMESPACE_NAME=${FULL_REMOTE_URL##*:}
    PROJECT_NAME=${NAMESPACE_NAME%%.git}
    if [ -z NAME ]; then
        echo "get namespace/project_name error, pls check your git config"
        exit 1
    fi
    SOURCE_BRANCH=`git symbolic-ref --short -q HEAD`
        if [ -z NAME ]; then
        echo "get source_branch error, pls check your git config"
        exit 1
    fi
}

getMergeRequestInfo() {
    getProject
    NAME_ENCODE=${PROJECT_NAME//\//%2F}
    ENCODE_URL="$GITLAB_API_ADDRESS/$GET_PROJECT_URL/$NAME_ENCODE"
    PROJECT_INFO_RESPONSE=`curl "$ENCODE_URL$AUTHENTICATION_URL"`
    MERGE_REQUESTS=$(echo $PROJECT_INFO_RESPONSE | grep -o -E $MERGE_REQUEST_PATTERN)
    if [ -z "$PROJECT_INFO_RESPONSE" ] || [ -z "$MERGE_REQUESTS" ]; then
        echo "get merge request url fail, pls check your git config"
        exit 1
    fi
    TMP_MERGER_URL=`echo "$PROJECT_INFO_RESPONSE" |grep -o -E "$MERGE_REQUEST_PATTERN"`
    MERGE_REQUEST_URL=`echo "$TMP_MERGER_URL"|grep -o -E "$HTTP_MERGE_REQUEST_PATTERN"`
    if [ -z "$MERGE_REQUEST_URL" ]; then
        echo "get real merge request url fail, pls check your git config"
        exit 1
    fi
}

do_mr(){
  TITLE=${@:2}
  if [ -z $1 -o -z TITLE ];then
     echo "title or target_branch can't be blank, pls check"
     exit 1
  fi
  getProject
  #获取远程分支前先fetch
  git fetch
  ALL_BRANCH=`git branch -r`
  #判断远程是否有当前分支配置的远程分支
  if [ -z `echo "$ALL_BRANCH" |grep -o -E "origin/$SOURCE_BRANCH$"` ];then
     echo "source_branch $SOURCE_BRANCH can't find in remote branch, pls check"
     exit 1
  fi
  #判断远程是否有目标分支
  if [ -z `echo "$ALL_BRANCH" |grep -o -E "origin/$1$"` ];then
     echo "target_branch $1 can't find in remote branch, pls check"
     exit 1
  fi
  getMergeRequestInfo
  echo "Creating Merge Request...   source_branch=$SOURCE_BRANCH and target_branch=$1"
  RESULT=`curl -d "description=$TITLE&title=$SOURCE_BRANCH&source_branch=$SOURCE_BRANCH&target_branch=$1" "$MERGE_REQUEST_URL$AUTHENTICATION_URL"`
  RESULT_MERGE_REQUEST_PATTERN="$GITLAB_ADDRESS$PROJECT_NAME/merge_requests/[0-9]+"
  RETURN_MERGE_REQUESTS_URL=`echo "$RESULT" |grep -o -E "$RESULT_MERGE_REQUEST_PATTERN"`
  if [ -z "$RETURN_MERGE_REQUESTS_URL" ];then
     echo "create Merge Request Fail, result is $RESULT"
     exit 1
  fi
  echo "create Merge Request Success. url = "$RETURN_MERGE_REQUESTS_URL
}

echo "Usage: " $(basename $0) "mr" "target_branch" "title"
eval do_$1 $2 $3
