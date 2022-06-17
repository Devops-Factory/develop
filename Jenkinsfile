//groovy script
def buildGithubCheck(repository, commitID, privateKey, status, checkName) {
    def currentTime = new Date().format("yyyy-MM-dd'T'HH:mm:ss'Z'")
    def checkName_run_id
  
    jsonWebToken = getJsonWebToken(privateKey)
    getStatusCode = validateAuth(jsonWebToken)
    if (!(getStatusCode in [200,201])) {
        error "Authentication request failed, status code: ${getStatusCode}"
    }
    token = getToken(jsonWebToken)
  
    try {
        checkName_run_id = getPreviousCheckNameRunID(repository, commitID, token, checkName)
    } catch(Exception e) {
        echo "Exception: ${e}"
        echo "Check name does not exist"
    }

    if (checkName_run_id) {
        getStatusCode = setCheckName(repository, checkName, status, currentTime, "PATCH", commitID, checkName_run_id)
    } else {
        getStatusCode = setCheckName(repository, checkName, status, previousDay, "POST", commitID)
    }
    if (!(getStatusCode in [200,201])) {
        error "Failed to create a check run, status code: ${getStatusCode}"
    }
}


APP_ID = 210149

def getJsonWebToken(privateKey) {
    try {
        privateCrtKey = getRSAPrivateKey(privateKey)
        time = accessTime()
        def jsonWebToken = Jwts.builder()
        .setSubject("RS256")
        .signWith(RS256, privateCrtKey)
        .setExpiration(time['expirationTime'])
        .setIssuedAt(time['iat'])
        .setIssuer(APP_ID)
        .compact()
        return jsonWebToken
    } catch(Exception e){
        echo "Exception: ${e}"
        error "Failed to create a JWT"
    }
}


def validateAuth(jsonWebToken) {
    try {
        def httpConn = new URL("https://api.github.com/app").openConnection();
        httpConn.setRequestProperty( 'Authorization', "Bearer ${jsonWebToken}" )
        httpConn.setRequestProperty( 'Accept', 'application/vnd.github.machine-man-preview+json' )
        return httpConn.getResponseCode();
    } catch(Exception e){
        echo "Exception: ${e}"
        error "Authentication request failed"
    }           
}
