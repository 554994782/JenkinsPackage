# JenkinsPackage
jenkins打包

//自动打包、上传蒲公英脚本
IPANAME="consumer"
EXPORTPLIST='/Users/shejijia/workspace/ExportOptions_QA.plist'
#EXPORTPLIST='/Users/shejijia/workspace/ExportOptions_AppStore.plist'

#React Native
export PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin
cd ./reactnative
npm install
cd ..

#Build & Archive
export PATH="$HOME/.fastlane/bin:$PATH"
security unlock-keychain -p 123456 ~/Library/Keychains/login.keychain
fastlane gym --workspace "shejijia.xcworkspace" --scheme "Shejijia-$PACKAGE_TYPE" --clean --output_directory "./Build" --export_options ${EXPORTPLIST} --output_name ${IPANAME}

#Upload to Pgy
cd ./Build
result=`curl -F "file=@${IPANAME}.ipa" -F "uKey=0c91f225b5229ebaad108bb2acc910da" -F "_api_key=bf32bedb5cfe74b375e751d959182e2a" https://qiniu-storage.pgyer.com/apiv1/app/upload`
echo $result
url=`echo $result | jq '.["data"]["appQRCodeURL"]' | sed 's/\"//g'`
curl $url -o ${IPANAME}.png


//Groovy Script
def build = Thread.currentThread().executable
build.displayName = manager.build.buildVariables.get("BRANCH_NAME") 
