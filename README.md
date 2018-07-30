# PermisssionUtils

public class PermissionsUtil {

    public final String TAG = "main";
    private static int PERMISSION_REQUEST_CODE;
    Activity activity;
    private static String[] NEEDED_PERMISSIONS;
    String[] strRemainingNeededPermissions;

    public static final int PERMISSION_REQUEST_CODE_CAMERA_STORAGE = 101;
    public static final int PERMISSION_REQUEST_CODE_PHONE_STATE = 102;
    public static final int PERMISSION_REQUEST_CODE_ALL = 104;

    public static String[] NEEDED_PERMS_ALL = { Manifest.permission.CAMERA, Manifest.permission.WRITE_EXTERNAL_STORAGE
    ,Manifest.permission.READ_PHONE_STATE,Manifest.permission.READ_SMS};
    public static String[] NEEDED_PERMS_CAMERA_STORAGE = { Manifest.permission.CAMERA, Manifest.permission.WRITE_EXTERNAL_STORAGE };
    public static String[] NEEDED_PERMS_PHONE_STATE = { Manifest.permission.READ_PHONE_STATE};


    public PermissionsUtil(){}

    public PermissionsUtil(Activity act){
        this.activity = act;
    }

    public void setNeededPermissions(String[] permissions){
        this.NEEDED_PERMISSIONS = permissions;
    }
    public void setPermissionsRequestCode(int permissionRequestCode){
        this.PERMISSION_REQUEST_CODE = permissionRequestCode;
    }

    public Boolean allRequiredPermissionsGranted(){
        for(String reqPermission : NEEDED_PERMISSIONS){
            if(ActivityCompat.checkSelfPermission(activity, reqPermission) != PackageManager.PERMISSION_GRANTED){
                return false;
            }
        }
        return true;
    }

    public void requestNeededPermissions() {

        List<String> remainingNeededPermissions = new ArrayList<String>();

        for(String neededPermission : NEEDED_PERMISSIONS){


            if(ActivityCompat.checkSelfPermission(activity, neededPermission) != PackageManager.PERMISSION_GRANTED){
                remainingNeededPermissions.add(neededPermission);
            }

        }


        strRemainingNeededPermissions = new String[remainingNeededPermissions.size()];
        strRemainingNeededPermissions = remainingNeededPermissions.toArray(strRemainingNeededPermissions);


        if( ActivityCompat.shouldShowRequestPermissionRationale(activity, NEEDED_PERMISSIONS[0]) ) {

            AlertDialog.Builder builderPerm = new AlertDialog.Builder(activity);
            builderPerm.setMessage("To continue, please \"allow\" the required  permissions.")
                    .setCancelable(false)
                    .setPositiveButton("OK", new DialogInterface.OnClickListener() {
                        public void onClick(DialogInterface dialog, int id) {
                            //Log.d(TAG, "OK is clicked in dialog");
                            doRequestPermission(strRemainingNeededPermissions);
                        }
                    });
            AlertDialog alertPerm = builderPerm.create();
            alertPerm.show();

        } else {
            //Log.d(TAG, "should NOT show rationale for " + NEEDED_PERMISSIONS[0]);
            doRequestPermission(strRemainingNeededPermissions);
        }

    }



    public Boolean processPermissionsRequestResult(int requestCode, String[] permissions, int[] grantResults){

        Boolean permissionsResult = verifyPermissionsResult(grantResults);
        if(permissionsResult){
            //Toast.makeText(activity, "Permission(s) granted. Please proceed.", Toast.LENGTH_SHORT).show();
            return true;
        } else {
            Toast.makeText(activity, "Please grant all permissions to proceed.", Toast.LENGTH_SHORT).show();
            return false;
        }

    }


    private void doRequestPermission(String[] requestThesePermissions){

        ActivityCompat.requestPermissions(activity, requestThesePermissions, PERMISSION_REQUEST_CODE);

    }

    /**
     * Check that all given permissions have been granted by verifying that each entry in the
     * given array is of the value {@link PackageManager#PERMISSION_GRANTED}.
     *
     * Source:https://github.com/googlesamples/android-RuntimePermissions
     *
     * @see Activity#onRequestPermissionsResult(int, String[], int[])
     */

    public static boolean verifyPermissionsResult(int[] grantResults) {
        if(grantResults.length < 1){
            return false;
        }
        for (int result : grantResults) {
            if (result != PackageManager.PERMISSION_GRANTED) {
                return false;
            }
        }
        return true;
    }


}


 PermissionsUtil permissionsUtil;
  /**
     * check hasPhoneState Permission or not
     * @return
     */
    public boolean hasPhoneStatePermisson(){
        permissionsUtil = new PermissionsUtil(this);
        boolean isPermission;
        if (Build.VERSION.SDK_INT >= 23) {
            permissionsUtil.setPermissionsRequestCode(PermissionsUtil.PERMISSION_REQUEST_CODE_PHONE_STATE);
            permissionsUtil.setNeededPermissions(PermissionsUtil.NEEDED_PERMS_PHONE_STATE);

            if(permissionsUtil.allRequiredPermissionsGranted()){
                isPermission = true;
            }else{
                isPermission = false;
                permissionsUtil.requestNeededPermissions();
            }

        }else{
            isPermission = true;
        }
        return isPermission;

    }
