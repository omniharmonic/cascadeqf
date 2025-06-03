import React, { useState, useEffect, createContext, useContext } from 'react';
import { ChevronRight, Settings, Users, BarChart, DollarSign, Filter, PlusCircle, Edit3, Trash2, Eye, CheckCircle, XCircle, LogIn, LogOut, ExternalLink, Search, Menu, X, Briefcase, LayoutDashboard, GitFork, HandCoins, CircleDollarSign, ArrowRightLeft, SlidersHorizontal, FileText, ShieldCheck, Database, Info, UserCircle2, Wallet, Landmark, Zap, Waves } from 'lucide-react'; // Added Waves

// Tailwind CSS is assumed to be available globally

const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
const firebaseConfig = typeof __firebase_config !== 'undefined' ? JSON.parse(__firebase_config) : {};

const AppContext = createContext();

// Updated Terminology: Meta-Pools -> Cascades, Grant Rounds/Sub-Pools -> Pools, Projects -> Grant Streams
const initialCascades = [
  { id: 'c1', name: 'Clean Environment Cascade', description: 'Aggregates funds to seed various environmental conservation Pools.', totalContributed: 12500, contributors: 150, status: 'Active - Accepting Contributions', defaultQFParams: { allocationMechanism: 'Quadratic Funding based on Proposals', minContributionProposal: 10, matchingCapPool: 5000, qfCoefficient: 1.5 }, fundedPools: [{id: 'p1', name: 'Reforestation Grant Streams Pool Q3', allocated: 5000 }] },
  { id: 'c2', name: 'Open Source Tech Cascade', description: 'Supports open-source innovation by funding specialized development Pools.', totalContributed: 8800, contributors: 95, status: 'Active - Accepting Contributions', defaultQFParams: { allocationMechanism: 'Manual Allocation', minContributionProposal: 0, matchingCapPool: 0, qfCoefficient: 0 }, fundedPools: [{id: 'p2', name: 'Decentralized Identity Pool', allocated: 3000}] },
  { id: 'c3', name: 'Community Health Cascade', description: 'Empowers local health initiatives through dedicated Pools.', totalContributed: 5200, contributors: 60, status: 'Allocation Pending', defaultQFParams: { allocationMechanism: 'Equal Distribution', minContributionProposal: 0, matchingCapPool: 0, qfCoefficient: 0 }, fundedPools: [] },
];

const initialPools = [ // Formerly GrantRounds
    { id: 'pool1', parentCascadeId: 'c1', name: 'Reforestation Grant Streams Pool Q3', description: 'Funding tree planting and forest restoration Grant Streams, seeded by the Clean Environment Cascade.', matchingFund: 5000, grantStreamCount: 3, contributors: 20, status: 'Open for Contributions', qfParams: { matchingMultiplier: 1.2, capPerContributor: 50, maxQFMatchPerGrantStream: 1000, minUniqueContributors: 5, minDirectFunding: 100 } },
    { id: 'pool2', parentCascadeId: 'c2', name: 'Decentralized Identity Pool', description: 'Supporting development of DID solutions, funded by the Open Source Tech Cascade.', matchingFund: 3000, grantStreamCount: 5, contributors: 15, status: 'Accepting Grant Stream Applications', qfParams: { matchingMultiplier: 1.5, capPerContributor: 100, maxQFMatchPerGrantStream: 1500, minUniqueContributors: 3, minDirectFunding: 50 } },
];

const initialGrantStreams = [ // Formerly Projects
    { id: 'gs1', poolId: 'pool1', title: 'Amazon Rainforest Reforestation Stream', blurb: 'Planting 10,000 trees in the Amazon.', directContributions: 1200, uniqueContributors: 15, estimatedQFMatch: 1800 },
    { id: 'gs2', poolId: 'pool1', title: 'Urban Green Spaces Initiative Stream', blurb: 'Creating green parks in city centers.', directContributions: 800, uniqueContributors: 10, estimatedQFMatch: 1000 },
];


function App() {
  const [currentPage, setCurrentPage] = useState('home');
  const [walletConnected, setWalletConnected] = useState(false);
  const [walletAddress, setWalletAddress] = useState('');
  const [isAdmin, setIsAdmin] = useState(false);
  const [showConnectWalletModal, setShowConnectWalletModal] = useState(false);
  const [showConfirmContributionModal, setShowConfirmContributionModal] = useState(false);
  const [contributionDetails, setContributionDetails] = useState({ to: '', amount: 0, type: '' }); // type: 'cascade', 'grantStream', 'pool'
  const [isMobileMenuOpen, setIsMobileMenuOpen] = useState(false);

  const [cascades, setCascades] = useState(initialCascades); // Renamed from metaPools
  const [pools, setPools] = useState(initialPools); // Renamed from grantRounds
  const [grantStreams, setGrantStreams] = useState(initialGrantStreams); // Renamed from projects
  const [systemSettings, setSystemSettings] = useState({
    defaultQFCoefficient: 1.0,
    platformContributionCap: 1000,
    supportedNetworks: "Ethereum Mainnet, Sepolia Testnet",
    rpcEndpoint: "https://mainnet.infura.io/v3/YOUR_KEY",
    feeOnContributions: 0.5, 
    feeOnMatchedFunds: 1.0, 
    adminUsers: [{ id: 'admin1', email: 'admin@cascadeqf.io' }] 
  });

  const navigate = (page) => {
    setCurrentPage(page);
    setIsMobileMenuOpen(false); 
    window.scrollTo(0, 0); 
  };

  const handleConnectWallet = () => {
    setShowConnectWalletModal(true);
  };

  const connectWallet = (address) => {
    setWalletConnected(true);
    setWalletAddress(address);
    setShowConnectWalletModal(false);
  };

  const disconnectWallet = () => {
    setWalletConnected(false);
    setWalletAddress('');
    setIsAdmin(false); 
  };

  const handleAdminLogin = (success) => {
    if (success) {
      setIsAdmin(true);
      navigate('adminDashboard');
    } else {
      alert("Admin login failed (mock). Use admin@cascadeqf.io / password");
    }
  };
  
  const handleAdminLogout = () => {
    setIsAdmin(false);
    navigate('home');
  };

  const openConfirmContributionModal = (to, amount, type) => {
    setContributionDetails({ to, amount, type });
    setShowConfirmContributionModal(true);
  };

  const handleConfirmContribution = () => {
    console.log(`Contribution of ${contributionDetails.amount} to ${contributionDetails.to} (${contributionDetails.type}) confirmed.`);
    
    if (contributionDetails.type === 'cascade') { // Updated type
        setCascades(prevCascades => prevCascades.map(cascade => 
            cascade.name === contributionDetails.to 
            ? { ...cascade, totalContributed: cascade.totalContributed + parseFloat(contributionDetails.amount), contributors: cascade.contributors + 1 }
            : cascade
        ));
    } else if (contributionDetails.type === 'grantStream') { // Updated type
        setGrantStreams(prevGrantStreams => prevGrantStreams.map(stream =>
            stream.title === contributionDetails.to
            ? { ...stream, directContributions: stream.directContributions + parseFloat(contributionDetails.amount), uniqueContributors: stream.uniqueContributors + 1, estimatedQFMatch: (stream.directContributions + parseFloat(contributionDetails.amount)) * 1.5 } 
            : stream
        ));
    } else if (contributionDetails.type === 'pool') { // Updated type
        setPools(prevPools => prevPools.map(pool =>
            pool.name === contributionDetails.to
            ? { ...pool, matchingFund: pool.matchingFund + parseFloat(contributionDetails.amount), contributors: pool.contributors + 1 }
            : pool
        ));
    }

    setShowConfirmContributionModal(false);
  };

  const appContextValue = {
    walletConnected,
    walletAddress,
    isAdmin,
    navigate,
    handleConnectWallet,
    disconnectWallet,
    handleAdminLogin,
    handleAdminLogout,
    openConfirmContributionModal,
    cascades, // Renamed
    setCascades, // Renamed
    pools, // Renamed
    setPools, // Renamed
    grantStreams, // Renamed
    setGrantStreams, // Renamed
    systemSettings,
    setSystemSettings,
  };

  const renderPage = () => {
    if (currentPage.startsWith('admin') && !isAdmin && currentPage !== 'adminLogin') { 
      return <AdminLoginPage />;
    }
    switch (currentPage) {
      case 'home': return <HomePage />;
      case 'exploreCascades': return <BrowseCascadesPage />; // Renamed
      case 'howItWorks': return <HowItWorksPage />; 
      case 'myProfile': return <MyProfilePage />;
      
      case 'adminLogin': return <AdminLoginPage />;
      case 'adminDashboard': return <AdminDashboardPage />;
      case 'adminManageCascades': return <AdminManageCascadesPage />; // Renamed
      case 'adminCreateCascade': return <AdminCreateEditCascadePage />; // Renamed
      case 'adminManagePools': return <AdminManagePoolsPage />; // Renamed
      case 'adminSystemSettings': return <AdminSystemSettingsPage />;
      default:
        if (currentPage.startsWith('cascadeDetail/')) { // Renamed
          const cascadeId = currentPage.split('/')[1];
          return <CascadeDetailPage cascadeId={cascadeId} />; // Renamed
        }
        if (currentPage.startsWith('adminEditCascade/')) { // Renamed
          const cascadeId = currentPage.split('/')[1];
          return <AdminCreateEditCascadePage editMode={true} cascadeId={cascadeId} />; // Renamed
        }
        if (currentPage.startsWith('adminInitiateAllocation/')) { 
          const cascadeId = currentPage.split('/')[1];
          return <AdminInitiateAllocationPage cascadeId={cascadeId} />; // Renamed
        }
        if (currentPage.startsWith('poolDetail/')) { // Renamed
            const poolId = currentPage.split('/')[1];
            return <PoolDetailPage poolId={poolId} />; // Renamed
        }
        if (currentPage.startsWith('grantStreamDetail/')) { // Renamed
            const streamId = currentPage.split('/')[1];
            return <GrantStreamDetailPage streamId={streamId} />; // Renamed
        }
         if (currentPage.startsWith('viewPools/')) { // Renamed
            const cascadeId = currentPage.split('/')[1];
            return <ViewPoolsPage cascadeId={cascadeId} />; // Renamed
        }
        if (currentPage.startsWith('adminCreatePool')) { // Renamed
            const parentCascadeId = currentPage.split('/')[1] || null;
            return <AdminCreateEditPoolPage parentCascadeIdParam={parentCascadeId} />; // Renamed
        }
        if (currentPage.startsWith('adminEditPool/')) { // Renamed
            const poolId = currentPage.split('/')[1];
            return <AdminCreateEditPoolPage editMode={true} poolId={poolId} />; // Renamed
        }
        return <HomePage />;
    }
  };
  
  // These state variables are used for navigation to detail pages, names are kept generic for now.
  const [currentCascadeId, setCurrentCascadeId] = useState(null); 
  const [currentAdminCascadeId, setCurrentAdminCascadeId] = useState(null); 
  const [currentAdminPoolId, setCurrentAdminPoolId] = useState(null); 


  return (
    <AppContext.Provider value={{ ...appContextValue, setCurrentCascadeId, setCurrentAdminCascadeId, setCurrentAdminPoolId }}>
      <div className="min-h-screen bg-slate-900 text-slate-100 font-sans">
        <Header onMobileMenuToggle={() => setIsMobileMenuOpen(!isMobileMenuOpen)} isMobileMenuOpen={isMobileMenuOpen} />
        <MobileMenu isOpen={isMobileMenuOpen} onClose={() => setIsMobileMenuOpen(false)} />
        
        <main className={`pt-16 transition-all duration-300 ease-in-out ${isAdmin ? 'sm:ml-64' : 'ml-0'}`}>
          <div className={`${isAdmin ? '' : 'p-4 md:p-8'}`}> 
            {renderPage()}
          </div>
        </main>

        {showConnectWalletModal && <ConnectWalletModal onClose={() => setShowConnectWalletModal(false)} onConnect={connectWallet} />}
        {showConfirmContributionModal && <ConfirmContributionModal onClose={() => setShowConfirmContributionModal(false)} details={contributionDetails} onConfirm={handleConfirmContribution} />}
        <Footer />
      </div>
    </AppContext.Provider>
  );
}

// --- Reusable Components ---
const Button = ({ children, onClick, variant = 'primary', className = '', iconLeft, iconRight, size = 'md', ...props }) => {
  const baseStyle = 'rounded-lg font-semibold focus:outline-none focus:ring-2 focus:ring-opacity-50 transition-all duration-150 ease-in-out shadow-md hover:shadow-lg disabled:opacity-50 disabled:cursor-not-allowed flex items-center justify-center space-x-2';
  let variantStyle = '';
  let sizeStyle = '';

  switch (variant) {
    case 'primary': variantStyle = 'bg-sky-500 hover:bg-sky-600 text-white focus:ring-sky-400'; break;
    case 'secondary': variantStyle = 'bg-slate-600 hover:bg-slate-700 text-sky-100 focus:ring-slate-500'; break;
    case 'danger': variantStyle = 'bg-red-500 hover:bg-red-600 text-white focus:ring-red-400'; break;
    case 'outline': variantStyle = 'bg-transparent border-2 border-sky-500 text-sky-400 hover:bg-sky-500 hover:text-white focus:ring-sky-400'; break;
    default: variantStyle = 'bg-sky-500 hover:bg-sky-600 text-white focus:ring-sky-400';
  }

  switch (size) {
    case 'sm': sizeStyle = 'px-3 py-1.5 text-xs'; break;
    case 'lg': sizeStyle = 'px-6 py-3 text-lg'; break;
    case 'md': 
    default: sizeStyle = 'px-4 py-2 text-sm'; break; 
  }
  return <button onClick={onClick} className={`${baseStyle} ${variantStyle} ${sizeStyle} ${className}`} {...props}>{iconLeft}{children}{iconRight}</button>;
};

const Card = ({ children, className = '' }) => {
  return <div className={`bg-slate-800 shadow-xl rounded-xl p-4 md:p-6 ${className}`}>{children}</div>;
};

const Input = ({ label, id, type = 'text', value, onChange, placeholder, className = '', required = false, iconLeft, helperText, ...props }) => {
  return (
    <div className="mb-4">
      {label && <label htmlFor={id} className="block text-sm font-medium text-slate-300 mb-1">{label}</label>}
      <div className="relative">
        {iconLeft && <div className="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">{iconLeft}</div>}
        <input 
          type={type} 
          id={id} 
          value={value} 
          onChange={onChange} 
          placeholder={placeholder} 
          required={required} 
          className={`w-full px-3 py-2 ${iconLeft ? 'pl-10' : ''} bg-slate-700 border border-slate-600 rounded-md shadow-sm focus:ring-sky-500 focus:border-sky-500 text-slate-100 placeholder-slate-400 ${className}`} 
          {...props} 
        />
      </div>
      {helperText && <p className="mt-1 text-xs text-slate-400">{helperText}</p>}
    </div>
  );
};

const Textarea = ({ label, id, value, onChange, placeholder, rows = 3, className = '', required = false }) => {
  return (
    <div className="mb-4">
      {label && <label htmlFor={id} className="block text-sm font-medium text-slate-300 mb-1">{label}</label>}
      <textarea id={id} value={value} onChange={onChange} placeholder={placeholder} rows={rows} required={required} className={`w-full px-3 py-2 bg-slate-700 border border-slate-600 rounded-md shadow-sm focus:ring-sky-500 focus:border-sky-500 text-slate-100 placeholder-slate-400 ${className}`} />
    </div>
  );
};

const Select = ({ label, id, value, onChange, children, className = '', required = false }) => {
  return (
    <div className="mb-4">
      {label && <label htmlFor={id} className="block text-sm font-medium text-slate-300 mb-1">{label}</label>}
      <select id={id} value={value} onChange={onChange} required={required} className={`w-full px-3 py-2 bg-slate-700 border border-slate-600 rounded-md shadow-sm focus:ring-sky-500 focus:border-sky-500 text-slate-100 ${className}`}>
        {children}
      </select>
    </div>
  );
};

const Modal = ({ children, onClose, title }) => {
  useEffect(() => {
    const handleEscape = (event) => {
      if (event.key === 'Escape') {
        onClose();
      }
    };
    document.addEventListener('keydown', handleEscape);
    return () => document.removeEventListener('keydown', handleEscape);
  }, [onClose]);

  return (
    <div className="fixed inset-0 bg-black bg-opacity-75 flex items-center justify-center z-50 p-4">
      <Card className="w-full max-w-lg max-h-[90vh] overflow-y-auto">
        <div className="flex justify-between items-center mb-4">
          <h2 className="text-2xl font-semibold text-sky-400">{title}</h2>
          <Button onClick={onClose} variant="secondary" size="sm" className="p-1 rounded-full -mr-2 -mt-2">
            <X size={20} />
          </Button>
        </div>
        {children}
      </Card>
    </div>
  );
};

// --- Layout Components ---
const Header = ({ onMobileMenuToggle, isMobileMenuOpen }) => {
  const { walletConnected, walletAddress, isAdmin, navigate, handleConnectWallet, disconnectWallet, handleAdminLogout } = useContext(AppContext);
  // IMPORTANT: Replace this placeholder with the actual URL of your hosted logo image.
  const logoUrl = "https://placehold.co/140x32/E0E7FF/1E293B?text=Allo.Capital&font=roboto"; // User needs to replace this


  return (
    <header className="bg-slate-800 shadow-lg fixed top-0 left-0 right-0 z-40 h-16 flex items-center justify-between px-4 md:px-6">
      <div className="flex items-center">
        {isAdmin && (
          <button onClick={onMobileMenuToggle} className="text-sky-400 hover:text-sky-300 mr-3 sm:hidden">
            {isMobileMenuOpen ? <X size={24} /> : <Menu size={24} />}
          </button>
        )}
        <div onClick={() => navigate(isAdmin ? 'adminDashboard' : 'home')} className="cursor-pointer flex items-center space-x-2">
          <img src={logoUrl} alt="Cascade QF by Allo.Capital Logo" className="h-8 w-auto" onError={(e) => e.target.style.display='none'} />
          <h1 className="text-lg md:text-xl font-bold text-sky-400 hidden sm:block">Cascade QF <span className="font-normal text-slate-400 text-sm">by Allo.Capital</span></h1>
        </div>
      </div>
      <nav className="hidden sm:flex items-center space-x-3 md:space-x-4">
        {!isAdmin && (
          <>
            <NavLink onClick={() => navigate('exploreCascades')}>Explore Cascades</NavLink> {/* Renamed */}
            <NavLink onClick={() => navigate('howItWorks')}>How it Works</NavLink>
          </>
        )}
        {walletConnected && !isAdmin && <NavLink onClick={() => navigate('myProfile')}>My Profile</NavLink>}
      </nav>
      <div className="flex items-center space-x-2 md:space-x-3">
        {walletConnected ? (
          <div className="flex items-center space-x-2">
            <span className="text-xs md:text-sm text-slate-300 hidden md:inline">{walletAddress.substring(0, 6)}...{walletAddress.substring(walletAddress.length - 4)}</span>
            <Button onClick={disconnectWallet} variant="secondary" size="sm">Disconnect</Button>
          </div>
        ) : (
          <Button onClick={handleConnectWallet} iconLeft={<Wallet size={16}/>} size="sm">Connect Wallet</Button>
        )}
        {isAdmin ? (
          <Button onClick={handleAdminLogout} variant="secondary" iconLeft={<LogOut size={16}/>} size="sm" className="hidden sm:flex">Admin Logout</Button>
        ) : (
          <Button onClick={() => navigate('adminLogin')} variant="outline" size="sm" className="hidden sm:flex">Admin</Button>
        )}
         <button onClick={onMobileMenuToggle} className="text-sky-400 hover:text-sky-300 sm:hidden">
            {!isAdmin ? (isMobileMenuOpen ? <X size={24} /> : <Menu size={24} />) : null }
          </button>
      </div>
    </header>
  );
};

const MobileMenu = ({ isOpen, onClose }) => {
  const { walletConnected, isAdmin, navigate, handleAdminLogout } = useContext(AppContext);

  if (!isOpen) return null;

  return (
    <div className="fixed inset-0 z-30 sm:hidden">
      <div className="fixed inset-0 bg-black/50" onClick={onClose}></div>
      <div className="fixed top-16 left-0 w-64 bg-slate-800 h-[calc(100vh-4rem)] shadow-xl p-4 overflow-y-auto">
        <nav className="flex flex-col space-y-3">
          {!isAdmin && (
            <>
              <NavLink onClick={() => { navigate('exploreCascades'); onClose(); }}>Explore Cascades</NavLink> {/* Renamed */}
              <NavLink onClick={() => { navigate('howItWorks'); onClose(); }}>How it Works</NavLink>
              {walletConnected && <NavLink onClick={() => { navigate('myProfile'); onClose(); }}>My Profile</NavLink>}
              <hr className="border-slate-700 my-2"/>
            </>
          )}
          {isAdmin ? (
            <>
              <AdminSidebarLinks onLinkClick={onClose} />
              <hr className="border-slate-700 my-2"/>
              <Button onClick={() => { handleAdminLogout(); onClose(); }} variant="secondary" iconLeft={<LogOut size={18}/>} className="w-full">Admin Logout</Button>
            </>
          ) : (
            <Button onClick={() => { navigate('adminLogin'); onClose(); }} variant="outline" className="w-full">Admin Login</Button>
          )}
        </nav>
      </div>
    </div>
  );
};


const NavLink = ({ children, onClick }) => (
  <button onClick={onClick} className="text-slate-300 hover:text-sky-400 transition-colors duration-150 font-medium px-2 py-1 text-sm">{children}</button>
);

const AdminSidebar = () => {
  return (
    <aside className="hidden sm:block fixed top-16 left-0 w-64 h-[calc(100vh-4rem)] bg-slate-800 shadow-md p-4 overflow-y-auto z-30">
      <nav className="flex flex-col space-y-2">
        <AdminSidebarLinks />
      </nav>
    </aside>
  );
};

const AdminSidebarLinks = ({ onLinkClick = () => {} }) => {
  const { navigate } = useContext(AppContext);
  const links = [
    { label: 'Dashboard', icon: <LayoutDashboard size={18}/>, page: 'adminDashboard' },
    { label: 'Cascades', icon: <Database size={18}/>, page: 'adminManageCascades' }, // Renamed
    { label: 'Pools', icon: <GitFork size={18}/>, page: 'adminManagePools' }, // Renamed
    { label: 'System Settings', icon: <SlidersHorizontal size={18}/>, page: 'adminSystemSettings' },
  ];

  return (
    <>
      {links.map(link => (
        <Button 
          key={link.page} 
          onClick={() => { navigate(link.page); onLinkClick(); }} 
          variant="secondary" 
          className="w-full justify-start !bg-transparent hover:!bg-slate-700 !text-slate-300 hover:!text-sky-400 !shadow-none"
          iconLeft={link.icon}
        >
          {link.label}
        </Button>
      ))}
    </>
  );
};


const Footer = () => {
  return (
    <footer className="bg-slate-800 text-slate-400 text-center p-6 mt-auto border-t border-slate-700">
      <p>&copy; {new Date().getFullYear()} Cascade QF by Allo.Capital. All rights reserved. (Mockup)</p>
      <p className="text-xs mt-1">This is a prototype and does not involve real transactions.</p>
    </footer>
  );
};

// --- SVG Flow Visualization Component ---
const FlowVisualization = ({ cascades, pools }) => { // Renamed props
    const [hoveredNode, setHoveredNode] = useState(null);
    const svgWidth = 800;
    const svgHeight = 550; // Increased height for better spacing

    const contributorNode = { x: 50, y: svgHeight / 2, label: "Contributors", type: "source", data: {} };
    
    const cascadeNodes = cascades.map((cascade, index) => ({ // Renamed
        id: cascade.id,
        x: 280, // Adjusted X for better spacing
        y: (svgHeight / (cascades.length + 1)) * (index + 1),
        label: cascade.name,
        type: "cascade", // Renamed
        data: cascade
    }));

    const poolNodes = []; // Renamed
    cascadeNodes.forEach(cNode => {
        const relatedPools = pools.filter(p => p.parentCascadeId === cNode.id); // Updated parent ID
        relatedPools.forEach((pool, index) => {
            poolNodes.push({
                id: pool.id,
                x: 550, // Adjusted X
                y: cNode.y - (relatedPools.length / 2 - index -0.5) * 70, // Increased Y spread
                label: pool.name,
                type: "pool", // Renamed
                data: pool,
                parentCascadeNode: cNode // Renamed
            });
        });
    });
    
    const grantStreamNode = { x: 780, y: svgHeight / 2, label: "Grant Streams", type: "sink", data: {} }; // Renamed

    const getStrokeWidth = (amount) => {
        if (!amount || amount <= 0) return 1.5; // Thinner base
        return Math.max(1.5, Math.min(12, Math.log10(amount) * 2.2)); 
    };
    
    const getWavyPath = (x1, y1, x2, y2, reverse = false) => {
        const midX = (x1 + x2) / 2;
        let cp1x = midX;
        let cp2x = midX;
        let cp1y = y1;
        let cp2y = y2;

        const dx = x2 - x1;
        const dy = y2 - y1;
        
        // Create more organic curves, inspired by hydrological flow
        if (reverse) { // For flows from right to left (not used here, but for future)
            cp1x = x1 - dx * 0.3;
            cp2x = x2 + dx * 0.3;
        } else { // For flows from left to right
            cp1x = x1 + dx * 0.4;
            cp2x = x2 - dx * 0.4;
        }
        
        // Add some vertical waviness
        const amplitude = Math.min(20, Math.abs(dy) * 0.1); // Smaller amplitude for straighter lines
        cp1y = y1 + (Math.random() - 0.5) * amplitude * 2;
        cp2y = y2 - (Math.random() - 0.5) * amplitude * 2;

        return `M${x1},${y1} C${cp1x},${cp1y} ${cp2x},${cp2y} ${x2},${y2}`;
    };

    return (
        <Card className="relative overflow-hidden bg-gradient-to-br from-slate-800 to-slate-850">
             <div className="absolute inset-0 opacity-10"> {/* Subtle background pattern */}
                <svg width="100%" height="100%" xmlns="http://www.w3.org/2000/svg">
                    <defs>
                        <pattern id="hydrology_pattern" patternUnits="userSpaceOnUse" width="80" height="80" patternTransform="scale(1) rotate(45)">
                            <path d="M0 40 Q 20 0 40 40 T 80 40" stroke="#0e7490" strokeWidth="0.5" fill="none"/>
                            <path d="M0 0 Q 20 80 40 0 T 80 0" stroke="#0e7490" strokeWidth="0.5" fill="none"/>
                        </pattern>
                    </defs>
                    <rect width="100%" height="100%" fill="url(#hydrology_pattern)" />
                </svg>
            </div>
            <div className="relative z-10"> {/* Content on top of pattern */}
                <h3 className="text-2xl font-semibold text-sky-300 mb-1 text-center">Visualizing the Cascade</h3>
                <p className="text-sm text-slate-400 mb-4 text-center">
                    Conceptual flow of funds from Contributors, through Cascades, to Pools, and finally to Grant Streams.
                    <br/>Line thickness and branching illustrate the distribution and impact. Hover for details.
                </p>
                <div className="bg-slate-700/50 backdrop-blur-sm p-2 rounded-lg overflow-x-auto">
                    <svg width="100%" height={svgHeight} viewBox={`0 0 ${svgWidth} ${svgHeight}`}>
                        <defs>
                            <marker id="arrowhead" markerWidth="8" markerHeight="6" refX="7" refY="3" orient="auto" markerUnits="strokeWidth">
                                <path d="M0,0 L8,3 L0,6 Z" fill="#22d3ee" />
                            </marker>
                            <filter id="glow" x="-50%" y="-50%" width="200%" height="200%">
                                <feGaussianBlur stdDeviation="3" result="coloredBlur"/>
                                <feMerge>
                                    <feMergeNode in="coloredBlur"/>
                                    <feMergeNode in="SourceGraphic"/>
                                </feMerge>
                            </filter>
                            <linearGradient id="flowGradient" x1="0%" y1="0%" x2="100%" y2="0%">
                                <stop offset="0%" stopColor="#06b6d4" />
                                <stop offset="100%" stopColor="#22d3ee" />
                            </linearGradient>
                        </defs>

                        {cascadeNodes.map(cNode => (
                            <path
                                key={`line-contrib-${cNode.id}`}
                                d={getWavyPath(contributorNode.x + 30, contributorNode.y, cNode.x - 20, cNode.y)}
                                stroke="url(#flowGradient)"
                                strokeWidth={getStrokeWidth(cNode.data.totalContributed)}
                                fill="none"
                                markerEnd="url(#arrowhead)"
                                className="transition-all duration-300"
                                style={{ filter: hoveredNode && (hoveredNode.id === cNode.id || hoveredNode.type === 'source') ? 'url(#glow)' : 'none', opacity: hoveredNode && hoveredNode.type !== 'source' && hoveredNode.id !== cNode.id && !poolNodes.find(p => p.parentCascadeNode.id === cNode.id && p.id === hoveredNode.id) ? 0.2 : 1 }}
                            />
                        ))}

                        {poolNodes.map(pNode => (
                            <path
                                key={`line-${pNode.parentCascadeNode.id}-${pNode.id}`}
                                d={getWavyPath(pNode.parentCascadeNode.x + 20, pNode.parentCascadeNode.y, pNode.x - 20, pNode.y)}
                                stroke="url(#flowGradient)"
                                strokeWidth={getStrokeWidth(pNode.data.matchingFund)}
                                fill="none"
                                markerEnd="url(#arrowhead)"
                                className="transition-all duration-300"
                                style={{ filter: hoveredNode && (hoveredNode.id === pNode.id || hoveredNode.id === pNode.parentCascadeNode.id) ? 'url(#glow)' : 'none', opacity: hoveredNode && hoveredNode.type !== 'source' && hoveredNode.id !== pNode.id && hoveredNode.id !== pNode.parentCascadeNode.id ? 0.2 : 1 }}
                            />
                        ))}
                        
                        {poolNodes.map(pNode => (
                            <path
                                key={`line-pool-${pNode.id}-streams`}
                                d={getWavyPath(pNode.x + 20, pNode.y, grantStreamNode.x - 30, grantStreamNode.y + (Math.random() -0.5) * (svgHeight/3.5) )}
                                stroke="url(#flowGradient)"
                                strokeOpacity="0.7"
                                strokeWidth={getStrokeWidth(pNode.data.matchingFund / (pNode.data.grantStreamCount || 1))}
                                fill="none"
                                markerEnd="url(#arrowhead)"
                                className="transition-all duration-300"
                                style={{ filter: hoveredNode && (hoveredNode.id === pNode.id || hoveredNode.type === 'sink') ? 'url(#glow)' : 'none', opacity: hoveredNode && hoveredNode.type !== 'sink' && hoveredNode.id !== pNode.id ? 0.2 : 1 }}
                            />
                        ))}

                        {[contributorNode, ...cascadeNodes, ...poolNodes, grantStreamNode].map(node => (
                            <g key={node.id || node.label} transform={`translate(${node.x}, ${node.y})`} 
                            onMouseEnter={() => setHoveredNode(node)}
                            onMouseLeave={() => setHoveredNode(null)}
                            className="cursor-pointer group"
                            >
                                <circle 
                                    r={node.type === 'source' || node.type === 'sink' ? 25 : node.type === 'cascade' ? 20 : 15} 
                                    fill={node.type === 'cascade' ? "rgba(6, 182, 212, 0.7)" : node.type === 'pool' ? "rgba(34, 211, 238, 0.7)" : "rgba(3, 105, 161, 0.8)"} 
                                    stroke="#38bdf8" 
                                    strokeWidth="1.5"
                                    className="transition-all duration-200 group-hover:stroke-sky-300 group-hover:stroke-[2.5px]"
                                    style={{ filter: hoveredNode && hoveredNode.id === node.id ? 'url(#glow)' : 'none' }}
                                />
                                <text x="0" y={node.type === 'source' || node.type === 'sink' ? 38 : node.type === 'cascade' ? 30 : 25} textAnchor="middle" fontSize="9px" fill="#cffafe" className="font-medium pointer-events-none select-none group-hover:fill-white">
                                    {node.label.length > 22 ? node.label.substring(0,20) + "..." : node.label}
                                </text>
                            </g>
                        ))}
                        {hoveredNode && (
                            <g transform={`translate(${Math.min(hoveredNode.x + 25, svgWidth - 200)}, ${Math.min(hoveredNode.y - 10, svgHeight - 60)})`} className="pointer-events-none">
                                <rect x="0" y="-15" width="190" 
                                      height={hoveredNode.type === 'pool' ? 65 : hoveredNode.type === 'cascade' ? 50 : 40} 
                                      fill="rgba(15, 23, 42, 0.95)" 
                                      stroke="#0ea5e9" rx="5" ry="5" 
                                      className="transition-opacity duration-150"
                                />
                                <text x="10" y="5" fontSize="12px" fill="#cffafe" fontWeight="bold">{hoveredNode.label}</text>
                                {hoveredNode.type === 'cascade' && (
                                    <text x="10" y="25" fontSize="10px" fill="#94a3b8">Total in Cascade: ${hoveredNode.data.totalContributed.toLocaleString()}</text>
                                )}
                                {hoveredNode.type === 'pool' && (
                                    <>
                                    <text x="10" y="25" fontSize="10px" fill="#94a3b8">Pool Matching Fund: ${hoveredNode.data.matchingFund.toLocaleString()}</text>
                                    <text x="10" y="40" fontSize="10px" fill="#94a3b8">Grant Streams: {hoveredNode.data.grantStreamCount}</text>
                                    </>
                                )}
                                {hoveredNode.type === 'source' && (
                                    <text x="10" y="25" fontSize="10px" fill="#94a3b8">Origin of all contributions.</text>
                                )}
                                {hoveredNode.type === 'sink' && (
                                    <text x="10" y="25" fontSize="10px" fill="#94a3b8">Funded Grant Streams creating impact.</text>
                                )}
                            </g>
                        )}
                    </svg>
                </div>
            </div>
        </Card>
    );
};


// --- Page Components ---
const HomePage = () => {
  const { navigate, cascades, pools } = useContext(AppContext); // Use new names
  const featuredCascades = cascades.slice(0, 2); // Use new name

  return (
    <div className="space-y-12">
      <section className="text-center py-12 md:py-20 bg-gradient-to-br from-slate-800 via-slate-850 to-slate-900 rounded-xl shadow-2xl">
        <h2 className="text-4xl md:text-5xl font-bold text-sky-400 mb-4">Amplify Your Impact with Cascade QF</h2>
        <p className="text-xl md:text-2xl text-slate-300 mb-8 max-w-3xl mx-auto">Fund the Future, Together.</p>
        <p className="text-slate-400 mb-10 max-w-2xl mx-auto">
          Cascade QF by Allo.Capital uses Quadratic Funding to democratically allocate resources. Contribute to broad Cascades, which then seed specific Pools for Grant Streams, creating a powerful cascade of community-driven support.
        </p>
        <div className="flex flex-col sm:flex-row justify-center space-y-3 sm:space-y-0 sm:space-x-4">
          <Button onClick={() => navigate('exploreCascades')} size="lg" iconRight={<ChevronRight size={20}/>}>Explore Cascades</Button> {/* Renamed */}
          <Button onClick={() => navigate('howItWorks')} variant="outline" size="lg" iconRight={<Info size={20}/>}>How Cascade QF Works</Button>
        </div>
      </section>

      <FlowVisualization cascades={cascades} pools={pools} /> {/* Use new names */}

      <section>
        <h3 className="text-3xl font-semibold text-sky-400 mb-6">Featured Cascades</h3> {/* Renamed */}
        <div className="grid md:grid-cols-2 gap-6">
          {featuredCascades.map(cascade => ( // Use new name
            <CascadeCard key={cascade.id} cascade={cascade} /> // Renamed
          ))}
        </div>
        {cascades.length > 2 && 
            <div className="mt-8 text-center">
                <Button onClick={() => navigate('exploreCascades')} variant="secondary" iconRight={<ChevronRight size={18}/>}>Explore All Cascades</Button> {/* Renamed */}
            </div>
        }
      </section>
    </div>
  );
};

const HowItWorksPage = () => { // Terminology updated here
    return (
        <Card className="max-w-4xl mx-auto">
            <h2 className="text-3xl md:text-4xl font-bold text-sky-400 mb-8 text-center">How Cascade QF Works</h2>
            
            <section className="mb-10">
                <h3 className="text-2xl font-semibold text-sky-500 mb-3 flex items-center">
                    <HandCoins size={28} className="mr-3"/>Quadratic Funding (QF)
                </h3>
                <p className="text-slate-300 leading-relaxed">
                    Quadratic Funding (QF) is a mathematically optimal way to fund public goods. It amplifies the impact of many small contributions over a few large ones, emphasizing broad community support.
                </p>
                <p className="text-slate-300 leading-relaxed mt-2">
                    The matching amount a Grant Stream (or a Pool proposal) receives from a matching fund is proportional to the <strong className="text-sky-300">square of the sum of the square roots</strong> of individual contributions.
                </p>
                 <Card className="mt-4 bg-slate-700 text-sm">
                    <p className="text-sky-300 font-semibold mt-1">Key takeaway: More unique contributors dramatically increase QF matching, not just the total amount donated.</p>
                </Card>
            </section>

            <section>
                <h3 className="text-2xl font-semibold text-sky-500 mb-3 flex items-center">
                    <Waves size={28} className="mr-3 text-teal-400"/>The Cascade QF Mechanism
                </h3>
                <p className="text-slate-300 leading-relaxed">
                    Cascade QF by Allo.Capital implements a multi-layered funding approach:
                </p>
                <ol className="list-decimal list-inside space-y-4 mt-4 text-slate-300 pl-4">
                    <li>
                        <strong className="text-sky-300">Contributors Fund Cascades:</strong> Individuals and organizations contribute to broad, thematic "Cascades" (e.g., "Clean Environment Cascade"). These aggregate capital for general causes.
                    </li>
                    <li>
                        <strong className="text-sky-300">Cascades Allocate to Pools:</strong>
                        The funds collected in Cascades are then allocated to create and seed more specific "Pools" (e.g., "Reforestation Pool").
                        <ul className="list-disc list-inside pl-6 mt-1 text-sm text-slate-400 space-y-1">
                            <li>This allocation from a Cascade to its Pools can itself be determined using QF (based on community proposals for new Pools) or other mechanisms.</li>
                            <li>The allocated amount from the Cascade becomes the <strong className="text-emerald-400">initial matching fund</strong> for the new Pool. This fund is dynamic, based on the Cascade's total at the time of allocation.</li>
                        </ul>
                    </li>
                    <li>
                        <strong className="text-sky-300">Grant Streams Apply to Pools:</strong> Specific "Grant Streams" (initiatives or projects) apply to participate in relevant Pools that align with their mission.
                    </li>
                    <li>
                        <strong className="text-sky-300">Contributors Fund Grant Streams within Pools:</strong> Once a Pool is active, contributors can donate directly to the Grant Streams participating in that Pool.
                    </li>
                    <li>
                        <strong className="text-sky-300">Grant Streams Receive QF Matching:</strong> Contributions made directly to Grant Streams are then matched from the Pool's dedicated matching fund (which was seeded by its parent Cascade). This matching is calculated using QF.
                    </li>
                </ol>
                <p className="text-slate-300 leading-relaxed mt-4">
                    This cascading model allows for both broad thematic funding and specific initiative support, all guided by community preferences and amplified by QF. The matching funds for Grant Streams are directly influenced by the success and allocation strategies of their parent Cascades and Pools.
                </p>
            </section>
            <div className="mt-10 text-center">
                <Button onClick={() => window.history.back()} variant="secondary">Go Back</Button>
            </div>
        </Card>
    );
};


const CascadeCard = ({ cascade }) => { // Renamed
  const { navigate, setCurrentCascadeId } = useContext(AppContext); // Renamed
  return (
    <Card className="hover:shadow-sky-500/30 transition-shadow duration-300 flex flex-col justify-between">
      <div>
        <h4 className="text-xl md:text-2xl font-semibold text-sky-400 mb-2">{cascade.name}</h4>
        <p className="text-slate-400 mb-4 text-sm leading-relaxed min-h-[4.5rem] overflow-hidden">{cascade.description}</p>
        <div className="text-xs text-slate-500 mb-1">Status: <span className={`font-semibold ${cascade.status.includes('Active') ? 'text-green-400' : 'text-yellow-400'}`}>{cascade.status}</span></div>
      </div>
      <div>
        <div className="flex justify-between items-center text-slate-300 mb-4">
          <div>
            <p className="text-lg font-semibold">${cascade.totalContributed.toLocaleString()}</p>
            <p className="text-xs text-slate-500">Total in Cascade</p>
          </div>
          <div>
            <p className="text-lg font-semibold">{cascade.contributors}</p>
            <p className="text-xs text-slate-500">Contributors</p>
          </div>
        </div>
        <div className="flex flex-col sm:flex-row space-y-2 sm:space-y-0 sm:space-x-2">
          <Button onClick={() => { setCurrentCascadeId(cascade.id); navigate(`cascadeDetail/${cascade.id}`); }} className="w-full" iconRight={<Eye size={16}/>}>View & Contribute</Button> {/* Renamed */}
          {cascade.fundedPools && cascade.fundedPools.length > 0 && ( // Renamed
            <Button onClick={() => navigate(`viewPools/${cascade.id}`)} variant="secondary" className="w-full" iconRight={<GitFork size={16}/>}>Funded Pools</Button> // Renamed
          )}
        </div>
      </div>
    </Card>
  );
};

const BrowseCascadesPage = () => { // Renamed
  const { cascades } = useContext(AppContext); // Renamed
  const [searchTerm, setSearchTerm] = useState('');

  const filteredCascades = cascades.filter(cascade => // Renamed
    cascade.name.toLowerCase().includes(searchTerm.toLowerCase()) ||
    cascade.description.toLowerCase().includes(searchTerm.toLowerCase())
  );

  return (
    <div className="space-y-8">
      <h2 className="text-3xl md:text-4xl font-bold text-sky-400 mb-6">Explore Cascades</h2> {/* Renamed */}
      <div className="mb-6 p-4 bg-slate-800 rounded-lg shadow">
        <Input 
            type="text" 
            placeholder="Search Cascades by name or description..." // Renamed
            value={searchTerm} 
            onChange={(e) => setSearchTerm(e.target.value)}
            className="!mb-0"
            iconLeft={<Search size={18} className="text-slate-400"/>}
        />
      </div>

      {filteredCascades.length > 0 ? (
        <div className="grid md:grid-cols-2 lg:grid-cols-3 gap-6">
          {filteredCascades.map(cascade => ( // Renamed
            <CascadeCard key={cascade.id} cascade={cascade} /> // Renamed
          ))}
        </div>
      ) : (
        <p className="text-slate-400 text-center py-10">No Cascades found matching your criteria.</p> // Renamed
      )}
    </div>
  );
};

const CascadeDetailPage = ({ cascadeId }) => { // Renamed
  const { cascades, walletConnected, openConfirmContributionModal, navigate, pools } = useContext(AppContext); // Renamed
  const [contributionAmount, setContributionAmount] = useState('');
  const [activeTab, setActiveTab] = useState('details'); 

  const cascade = cascades.find(c => c.id === cascadeId); // Renamed

  if (!cascade) {
    return <div className="text-center text-red-400 py-10">Cascade not found. <Button onClick={() => navigate('exploreCascades')}>Back to Cascades</Button></div>; // Renamed
  }
  
  const relatedPools = pools.filter(p => p.parentCascadeId === cascade.id); // Renamed

  const handleContribute = () => {
    if (!walletConnected) {
      alert("Please connect your wallet first."); 
      return;
    }
    if (parseFloat(contributionAmount) <= 0 || isNaN(parseFloat(contributionAmount))) {
      alert("Please enter a valid contribution amount.");
      return;
    }
    openConfirmContributionModal(cascade.name, contributionAmount, 'cascade'); // Updated type
  };

  return (
    <Card className="max-w-4xl mx-auto">
      <div className="mb-6 pb-4 border-b border-slate-700">
        <h2 className="text-3xl md:text-4xl font-bold text-sky-400 mb-2">{cascade.name}</h2>
        <p className="text-slate-400 leading-relaxed">{cascade.description}</p>
      </div>

      <div className="flex border-b border-slate-700 mb-6">
        <button 
            onClick={() => setActiveTab('details')} 
            className={`py-2 px-4 font-medium ${activeTab === 'details' ? 'text-sky-400 border-b-2 border-sky-400' : 'text-slate-400 hover:text-sky-300'}`}
        >
            Details & Contribute
        </button>
        <button 
            onClick={() => setActiveTab('fundedPools')} 
            className={`py-2 px-4 font-medium ${activeTab === 'fundedPools' ? 'text-sky-400 border-b-2 border-sky-400' : 'text-slate-400 hover:text-sky-300'}`}
        >
            Funded Pools ({relatedPools.length}) {/* Renamed */}
        </button>
      </div>

      {activeTab === 'details' && (
        <div className="grid md:grid-cols-2 gap-8">
          <div>
            <h3 className="text-xl font-semibold text-sky-500 mb-3">Cascade Statistics</h3> {/* Renamed */}
            <div className="space-y-2 text-slate-300">
              <p><strong>Total in Cascade:</strong> ${cascade.totalContributed.toLocaleString()}</p> {/* Renamed */}
              <p><strong>Unique Contributors:</strong> {cascade.contributors}</p>
              <p><strong>Status:</strong> <span className={`${cascade.status.includes('Active') ? 'text-green-400' : 'text-yellow-400'}`}>{cascade.status}</span></p>
              <p className="text-sm text-slate-400 mt-2">
                <Info size={16} className="inline mr-1 mb-0.5"/>
                Funds in this Cascade are allocated to seed specific Pools. Higher contributions here mean more potential funding for those Pools. {/* Renamed */}
              </p>
            </div>
          </div>
          <div>
            <h3 className="text-xl font-semibold text-sky-500 mb-3">Contribute to this Cascade</h3> {/* Renamed */}
            <Input 
              label="Amount (ETH/DAI - mock)" 
              id="contributionAmount" 
              type="number" 
              value={contributionAmount} 
              onChange={(e) => setContributionAmount(e.target.value)} 
              placeholder="e.g., 10" 
            />
            <p className="text-xs text-slate-400 mb-4">
              Your contribution increases the total funds in this Cascade, which will be used to create and fund Pools. More contributors and larger funds here amplify the potential of the entire system. {/* Renamed */}
            </p>
            <Button onClick={handleContribute} disabled={!walletConnected || !cascade.status.includes('Active')} className="w-full" iconLeft={<HandCoins size={18}/>}>
              {walletConnected ? 'Contribute to Cascade' : 'Connect Wallet to Contribute'} {/* Renamed */}
            </Button>
            {!cascade.status.includes('Active') && <p className="text-yellow-400 text-sm mt-2 text-center">This Cascade is not currently accepting contributions.</p>} {/* Renamed */}
          </div>
        </div>
      )}

      {activeTab === 'fundedPools' && ( // Renamed
        <div>
          <h3 className="text-xl font-semibold text-sky-500 mb-4">Pools Funded by {cascade.name}</h3> {/* Renamed */}
          {relatedPools.length > 0 ? (
            <div className="space-y-4">
              {relatedPools.map(p => ( // Renamed variable
                <PoolCard key={p.id} pool={p} /> // Renamed component and prop
              ))}
            </div>
          ) : (
            <p className="text-slate-400">This Cascade has not funded any Pools yet.</p> // Renamed
          )}
        </div>
      )}
      <div className="mt-8 pt-4 border-t border-slate-700">
        <Button onClick={() => navigate('exploreCascades')} variant="secondary" iconLeft={<ChevronRight size={16} className="transform rotate-180"/>}>Back to All Cascades</Button> {/* Renamed */}
      </div>
    </Card>
  );
};


const ViewPoolsPage = ({ cascadeId }) => { // Renamed
    const { navigate, pools, cascades } = useContext(AppContext); // Renamed
    const parentCascade = cascades.find(c => c.id === cascadeId); // Renamed
    const childPools = pools.filter(p => p.parentCascadeId === cascadeId); // Renamed

    if (!parentCascade) {
        return <div className="text-center text-red-400 py-10">Parent Cascade not found. <Button onClick={() => navigate('exploreCascades')}>Back to Cascades</Button></div>; // Renamed
    }

    return (
        <div className="space-y-8">
            <h2 className="text-3xl md:text-4xl font-bold text-sky-400 mb-2">Pools Funded by: {parentCascade.name}</h2> {/* Renamed */}
            <p className="text-slate-400 mb-6">These Pools were created and received their initial matching funds through allocations from the "{parentCascade.name}" Cascade.</p> {/* Renamed */}
            
            {childPools.length > 0 ? (
                <div className="grid md:grid-cols-2 gap-6">
                    {childPools.map(pool => ( // Renamed
                        <PoolCard key={pool.id} pool={pool} /> // Renamed
                    ))}
                </div>
            ) : (
                <Card>
                    <p className="text-slate-300 text-center py-8">This Cascade has not yet funded any Pools.</p> {/* Renamed */}
                </Card>
            )}
            <div className="mt-8">
                <Button onClick={() => navigate(`cascadeDetail/${cascadeId}`)} variant="secondary" iconLeft={<ChevronRight size={16} className="transform rotate-180"/>}>Back to {parentCascade.name}</Button> {/* Renamed */}
            </div>
        </div>
    );
};

const PoolCard = ({ pool }) => { // Renamed
    const { navigate, cascades } = useContext(AppContext); // Renamed
    const parentCascade = cascades.find(c => c.id === pool.parentCascadeId); // Renamed
    return (
        <Card className="hover:shadow-sky-500/30 transition-shadow duration-300 flex flex-col justify-between">
            <div>
                <h4 className="text-xl font-semibold text-sky-400 mb-1">{pool.name}</h4>
                {parentCascade && <p className="text-xs text-slate-500 mb-1">Seeded by: {parentCascade.name}</p>} {/* Renamed */}
                <p className="text-slate-400 mb-3 text-sm leading-relaxed min-h-[3em] overflow-hidden">{pool.description}</p>
                <div className="text-xs text-slate-500 mb-1">Status: <span className={`font-semibold ${pool.status.includes('Open') || pool.status.includes('Accepting') ? 'text-green-400' : 'text-yellow-400'}`}>{pool.status}</span></div>
            </div>
            <div>
                <div className="flex justify-between items-center text-slate-300 mb-3 text-sm">
                    <div>
                        <p className="font-semibold">${pool.matchingFund.toLocaleString()}</p>
                        <p className="text-xs text-slate-500">Pool Matching Fund</p> {/* Renamed */}
                    </div>
                    <div>
                        <p className="font-semibold">{pool.grantStreamCount}</p> {/* Renamed */}
                        <p className="text-xs text-slate-500">Grant Streams</p> {/* Renamed */}
                    </div>
                     <div>
                        <p className="font-semibold">{pool.contributors}</p>
                        <p className="text-xs text-slate-500">Pool Contributors</p> {/* Renamed */}
                    </div>
                </div>
                <Button onClick={() => navigate(`poolDetail/${pool.id}`)} className="w-full" size="sm" iconRight={<Eye size={16}/>}>View Pool & Grant Streams</Button> {/* Renamed */}
            </div>
        </Card>
    );
};


const PoolDetailPage = ({ poolId }) => { // Renamed
    const { pools, grantStreams, navigate, openConfirmContributionModal, walletConnected, cascades } = useContext(AppContext); // Renamed
    const [contributionAmount, setContributionAmount] = useState('');
    const currentPool = pools.find(p => p.id === poolId); // Renamed
    const poolGrantStreams = grantStreams.filter(gs => gs.poolId === poolId); // Renamed
    const parentCascade = currentPool ? cascades.find(c => c.id === currentPool.parentCascadeId) : null; // Renamed


    if (!currentPool) {
        return <div className="text-center text-red-400 py-10">Pool not found. <Button onClick={() => navigate('exploreCascades')}>Back to Cascades</Button></div>; // Renamed
    }
    
    const handleContributeToPoolFund = () => { // Renamed
        if (!walletConnected) {
          alert("Please connect your wallet first."); return;
        }
        if (parseFloat(contributionAmount) <= 0 || isNaN(parseFloat(contributionAmount))) {
          alert("Please enter a valid contribution amount."); return;
        }
        openConfirmContributionModal(currentPool.name, contributionAmount, 'pool'); // Updated type
    };


    return (
        <Card className="max-w-5xl mx-auto">
            <div className="mb-6 pb-4 border-b border-slate-700">
                <h2 className="text-3xl md:text-4xl font-bold text-sky-400 mb-2">{currentPool.name}</h2>
                <p className="text-slate-400 leading-relaxed mb-1">{currentPool.description}</p>
                {parentCascade && <p className="text-sm text-slate-500">This Pool was seeded with funds from the <strong className="text-sky-400">{parentCascade.name}</strong> Cascade.</p>} {/* Renamed */}
            </div>

            <div className="grid md:grid-cols-3 gap-6 mb-8">
                <div className="bg-slate-700 p-4 rounded-lg">
                    <p className="text-xs text-slate-400">Total Matching Fund for this Pool</p> {/* Renamed */}
                    <p className="text-2xl font-semibold text-sky-400">${currentPool.matchingFund.toLocaleString()}</p>
                </div>
                <div className="bg-slate-700 p-4 rounded-lg">
                    <p className="text-xs text-slate-400">Participating Grant Streams</p> {/* Renamed */}
                    <p className="text-2xl font-semibold text-sky-400">{poolGrantStreams.length}</p>
                </div>
                <div className="bg-slate-700 p-4 rounded-lg">
                    <p className="text-xs text-slate-400">Status</p>
                    <p className={`text-lg font-semibold ${currentPool.status.includes('Open') || currentPool.status.includes('Accepting') ? 'text-green-400' : 'text-yellow-400'}`}>{currentPool.status}</p>
                </div>
            </div>
            
            {currentPool.status.includes("Open for Contributions") && (
              <Card className="mb-8 bg-slate-750">
                <h3 className="text-xl font-semibold text-sky-500 mb-3">Boost This Pool's Matching Fund</h3> {/* Renamed */}
                <p className="text-sm text-slate-400 mb-3">Further increase the matching funds available for Grant Streams in this Pool. Your contribution here directly adds to this Pool's fund.</p> {/* Renamed */}
                <div className="flex items-end space-x-3">
                    <Input 
                        label="Amount (ETH/DAI - mock)" 
                        id="poolContributionAmount" // Renamed
                        type="number" 
                        value={contributionAmount} 
                        onChange={(e) => setContributionAmount(e.target.value)} 
                        placeholder="e.g., 5"
                        className="flex-grow !mb-0"
                    />
                    <Button onClick={handleContributeToPoolFund} disabled={!walletConnected} iconLeft={<HandCoins size={18}/>}>
                        {walletConnected ? 'Contribute to Pool Fund' : 'Connect Wallet'} {/* Renamed */}
                    </Button>
                </div>
              </Card>
            )}


            <h3 className="text-2xl font-semibold text-sky-500 mb-4">Grant Streams in this Pool</h3> {/* Renamed */}
            {currentPool.status.includes("Accepting Grant Stream Applications") && ( // Renamed
                <div className="mb-6 text-center">
                    <Button onClick={() => alert("Grant Stream submission form would appear here.")} iconLeft={<PlusCircle size={18}/>}>Submit Your Grant Stream to this Pool</Button> {/* Renamed */}
                </div>
            )}

            {poolGrantStreams.length > 0 ? (
                <div className="space-y-4">
                    {poolGrantStreams.map(gs => ( // Renamed
                        <GrantStreamCard key={gs.id} grantStream={gs} poolName={currentPool.name} /> // Renamed
                    ))}
                </div>
            ) : (
                <p className="text-slate-400 text-center py-6">No Grant Streams currently in this Pool.</p> // Renamed
            )}
             <div className="mt-8 pt-4 border-t border-slate-700">
                <Button onClick={() => navigate(currentPool.parentCascadeId ? `viewPools/${currentPool.parentCascadeId}` : 'exploreCascades')} variant="secondary" iconLeft={<ChevronRight size={16} className="transform rotate-180"/>}>Back to {parentCascade ? parentCascade.name : 'Cascades'}</Button> {/* Renamed */}
            </div>
        </Card>
    );
};

const GrantStreamCard = ({ grantStream, poolName }) => { // Renamed
    const { navigate } = useContext(AppContext);
    return (
        <Card className="bg-slate-700 hover:bg-slate-650 transition-colors duration-200">
            <div className="flex flex-col md:flex-row justify-between md:items-center mb-3">
                <h4 className="text-xl font-semibold text-sky-400">{grantStream.title}</h4>
                <p className="text-xs text-slate-500">In Pool: {poolName}</p> {/* Renamed */}
            </div>
            <p className="text-sm text-slate-300 mb-4 min-h-[2.5em] overflow-hidden">{grantStream.blurb}</p>
            <div className="grid grid-cols-2 md:grid-cols-3 gap-3 text-sm mb-4">
                <div>
                    <p className="text-xs text-slate-400">Direct Contributions</p>
                    <p className="font-semibold text-slate-200">${grantStream.directContributions.toLocaleString()}</p>
                </div>
                <div>
                    <p className="text-xs text-slate-400">Unique Contributors</p>
                    <p className="font-semibold text-slate-200">{grantStream.uniqueContributors}</p>
                </div>
                <div className="col-span-2 md:col-span-1">
                    <p className="text-xs text-slate-400">Est. QF Match from Pool</p> {/* Renamed */}
                    <p className="font-semibold text-green-400">${grantStream.estimatedQFMatch.toLocaleString()}</p>
                </div>
            </div>
            <Button onClick={() => navigate(`grantStreamDetail/${grantStream.id}`)} className="w-full md:w-auto" size="sm" iconRight={<Eye size={16}/>}>View Details & Contribute</Button> {/* Renamed */}
        </Card>
    );
};

const GrantStreamDetailPage = ({ streamId }) => { // Renamed
    const { grantStreams, pools, walletConnected, openConfirmContributionModal, navigate } = useContext(AppContext); // Renamed
    const [contributionAmount, setContributionAmount] = useState('');
    
    const grantStream = grantStreams.find(gs => gs.id === streamId); // Renamed

    if (!grantStream) {
        return <div className="text-center text-red-400 py-10">Grant Stream not found. <Button onClick={() => navigate('exploreCascades')}>Back to Cascades</Button></div>; // Renamed
    }
    const pool = pools.find(p => p.id === grantStream.poolId); // Renamed


    const handleContribute = () => {
        if (!walletConnected) {
          alert("Please connect your wallet first."); return;
        }
        if (parseFloat(contributionAmount) <= 0 || isNaN(parseFloat(contributionAmount))) {
          alert("Please enter a valid contribution amount."); return;
        }
        openConfirmContributionModal(grantStream.title, contributionAmount, 'grantStream'); // Updated type
    };

    return (
        <Card className="max-w-3xl mx-auto">
            <div className="mb-6 pb-4 border-b border-slate-700">
                <h2 className="text-3xl font-bold text-sky-400 mb-2">{grantStream.title}</h2>
                <p className="text-sm text-slate-500">Part of Pool: {pool ? pool.name : 'Unknown Pool'}</p> {/* Renamed */}
            </div>

            <div className="mb-6">
                <img src={`https://placehold.co/800x400/1E293B/94A3B8?text=${encodeURIComponent(grantStream.title)}`} alt={grantStream.title} className="rounded-lg mb-4 w-full h-auto object-cover"/>
                <h3 className="text-xl font-semibold text-sky-500 mb-2">Grant Stream Description</h3> {/* Renamed */}
                <p className="text-slate-300 leading-relaxed mb-2">{grantStream.blurb} This is a more detailed mock description for the Grant Stream. It would outline its goals, methods, and expected impact. Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.</p> {/* Renamed */}
                <h3 className="text-xl font-semibold text-sky-500 mb-2 mt-4">Team/Creator</h3>
                <p className="text-slate-300">Mock Team Lead, Mock Developer, Mock Designer</p>
            </div>
            
            <Card className="bg-slate-700 mb-6">
                <h3 className="text-xl font-semibold text-sky-500 mb-3">Funding Progress</h3>
                <div className="grid grid-cols-2 gap-4 text-slate-200">
                    <div>
                        <p className="text-xs text-slate-400">Direct Contributions</p>
                        <p className="text-2xl font-bold">${grantStream.directContributions.toLocaleString()}</p>
                    </div>
                    <div>
                        <p className="text-xs text-slate-400">Unique Contributors</p>
                        <p className="text-2xl font-bold">{grantStream.uniqueContributors}</p>
                    </div>
                    <div>
                        <p className="text-xs text-slate-400">Est. QF Matching (from Pool)</p> {/* Renamed */}
                        <p className="text-2xl font-bold text-green-400">${grantStream.estimatedQFMatch.toLocaleString()}</p>
                    </div>
                    <div>
                        <p className="text-xs text-slate-400">Total (Direct + Est. Match)</p>
                        <p className="text-2xl font-bold text-sky-400">${(grantStream.directContributions + grantStream.estimatedQFMatch).toLocaleString()}</p>
                    </div>
                </div>
            </Card>

            <Card className="bg-slate-700">
                 <h3 className="text-xl font-semibold text-sky-500 mb-3">Contribute to this Grant Stream</h3> {/* Renamed */}
                <Input 
                  label="Amount (ETH/DAI - mock)" 
                  id="grantStreamContributionAmount" // Renamed
                  type="number" 
                  value={contributionAmount} 
                  onChange={(e) => setContributionAmount(e.target.value)} 
                  placeholder="e.g., 5" 
                />
                <p className="text-xs text-slate-400 mb-4">
                  Contribute to '{grantStream.title}'. Every contribution increases its QF matching from the '{pool ? pool.name : ''}' Pool. Your $10 could unlock significant matching! {/* Renamed */}
                </p>
                <Button onClick={handleContribute} disabled={!walletConnected} className="w-full" iconLeft={<HandCoins size={18}/>}>
                  {walletConnected ? 'Contribute to Grant Stream' : 'Connect Wallet to Contribute'} {/* Renamed */}
                </Button>
            </Card>

            <div className="mt-8 pt-6 border-t border-slate-700">
                <h3 className="text-xl font-semibold text-sky-500 mb-3">Updates & Milestones</h3>
                <p className="text-slate-400 text-sm">No updates yet. (Mock section)</p>
                 <h3 className="text-xl font-semibold text-sky-500 mb-3 mt-4">Comments</h3>
                <p className="text-slate-400 text-sm">No comments yet. (Mock section)</p>
            </div>
             <div className="mt-8 pt-4 border-t border-slate-700">
                <Button onClick={() => navigate(pool ? `poolDetail/${pool.id}` : 'exploreCascades')} variant="secondary" iconLeft={<ChevronRight size={16} className="transform rotate-180"/>}>Back to {pool ? pool.name : 'Pools'}</Button> {/* Renamed */}
            </div>
        </Card>
    );
};


const MyProfilePage = () => {
  const { walletAddress } = useContext(AppContext);
  const myContributions = [
    { id: 'c1', date: '2024-05-15', amount: 25, to: 'Clean Environment Cascade', txId: '0x123...' }, // Renamed
    { id: 'c2', date: '2024-05-20', amount: 10, to: 'Amazon Rainforest Reforestation Stream', txId: '0x456...' }, // Renamed
  ];
  const myGrantStreams = [ // Renamed
    { id: 'mgs1', title: 'My Awesome Grant Stream', status: 'Active', fundsReceivedDirect: 500, fundsReceivedMatched: 750 }, // Renamed
  ];
  const [activeTab, setActiveTab] = useState('contributions');

  return (
    <Card className="max-w-3xl mx-auto">
      <div className="flex items-center mb-6 pb-4 border-b border-slate-700">
        <UserCircle2 size={48} className="text-sky-400 mr-4"/>
        <div>
            <h2 className="text-3xl font-bold text-sky-400">My Profile</h2>
            <p className="text-sm text-slate-400">Wallet: {walletAddress}</p>
        </div>
      </div>
      
      <div className="flex border-b border-slate-700 mb-6">
        <button 
            onClick={() => setActiveTab('contributions')} 
            className={`py-2 px-4 font-medium ${activeTab === 'contributions' ? 'text-sky-400 border-b-2 border-sky-400' : 'text-slate-400 hover:text-sky-300'}`}
        >
            My Contributions
        </button>
        <button 
            onClick={() => setActiveTab('grantStreams')}  // Renamed
            className={`py-2 px-4 font-medium ${activeTab === 'grantStreams' ? 'text-sky-400 border-b-2 border-sky-400' : 'text-slate-400 hover:text-sky-300'}`}
        >
            My Grant Streams {/* Renamed */}
        </button>
      </div>

      {activeTab === 'contributions' && (
        <div>
          <h3 className="text-xl font-semibold text-sky-500 mb-4">My Contributions</h3>
          {myContributions.length > 0 ? (
            <ul className="space-y-3">
              {myContributions.map(c => (
                <li key={c.id} className="p-3 bg-slate-700 rounded-md text-sm">
                  <p><strong>Date:</strong> {c.date} | <strong>Amount:</strong> ${c.amount}</p>
                  <p><strong>To:</strong> {c.to}</p>
                  <p><strong>TxID (mock):</strong> <a href="#" className="text-sky-400 hover:underline">{c.txId}</a></p>
                </li>
              ))}
            </ul>
          ) : <p className="text-slate-400">You haven't made any contributions yet.</p>}
        </div>
      )}

      {activeTab === 'grantStreams' && ( // Renamed
        <div>
          <h3 className="text-xl font-semibold text-sky-500 mb-4">My Grant Streams</h3> {/* Renamed */}
           {myGrantStreams.length > 0 ? ( // Renamed
            <ul className="space-y-3">
              {myGrantStreams.map(gs => ( // Renamed
                <li key={gs.id} className="p-3 bg-slate-700 rounded-md text-sm">
                  <p><strong>Title:</strong> {gs.title} | <strong>Status:</strong> <span className="text-green-400">{gs.status}</span></p>
                  <p><strong>Funds Received (Direct):</strong> ${gs.fundsReceivedDirect}</p>
                  <p><strong>Funds Received (Matched):</strong> ${gs.fundsReceivedMatched}</p>
                  <Button variant="outline" size="sm" className="mt-2 text-xs py-1">Manage Grant Stream</Button> {/* Renamed */}
                </li>
              ))}
            </ul>
          ) : <p className="text-slate-400">You haven't submitted any Grant Streams yet.</p>} {/* Renamed */}
        </div>
      )}
    </Card>
  );
};


// --- Modals ---
const ConnectWalletModal = ({ onClose, onConnect }) => {
  const [connecting, setConnecting] = useState(false);
  const [connected, setConnected] = useState(false);

  const handleConnect = (walletName) => {
    setConnecting(true);
    setTimeout(() => {
      setConnecting(false);
      setConnected(true);
      const mockAddress = `0x${Array(40).fill(0).map(() => Math.floor(Math.random() * 16).toString(16)).join('')}`;
      setTimeout(() => onConnect(mockAddress), 500); 
    }, 1500);
  };

  return (
    <Modal onClose={onClose} title="Connect Your Wallet">
      {!connecting && !connected && (
        <div className="space-y-3">
          <p className="text-slate-300 text-sm">Select your preferred wallet to continue.</p>
          <Button onClick={() => handleConnect('MetaMask')} className="w-full justify-start" iconLeft={<Landmark size={20} className="mr-2"/>}>MetaMask (Recommended)</Button>
          <Button onClick={() => handleConnect('WalletConnect')} className="w-full justify-start !bg-slate-600 hover:!bg-slate-700" iconLeft={<Landmark size={20} className="mr-2"/>}>WalletConnect</Button>
          <Button onClick={() => handleConnect('Coinbase Wallet')} className="w-full justify-start !bg-slate-600 hover:!bg-slate-700" iconLeft={<Landmark size={20} className="mr-2"/>}>Coinbase Wallet</Button>
          <p className="text-xs text-slate-500 pt-2">By connecting your wallet, you agree to our Terms of Service (mock).</p>
        </div>
      )}
      {connecting && (
        <div className="text-center py-8">
          <div className="animate-spin rounded-full h-12 w-12 border-b-2 border-sky-400 mx-auto mb-4"></div>
          <p className="text-slate-300">Connecting to MetaMask...</p>
        </div>
      )}
      {connected && (
         <div className="text-center py-8">
          <CheckCircle size={48} className="text-green-400 mx-auto mb-4"/>
          <p className="text-slate-200 text-lg font-semibold">Connected!</p>
          <p className="text-slate-400 text-sm">You will be redirected shortly.</p>
        </div>
      )}
    </Modal>
  );
};

const ConfirmContributionModal = ({ onClose, details, onConfirm }) => {
  const [processing, setProcessing] = useState(false);
  const [confirmed, setConfirmed] = useState(false);
  const mockGasFee = 0.0025; 

  const handleConfirmClick = () => { 
    setProcessing(true);
    setTimeout(() => {
      setProcessing(false);
      setConfirmed(true);
      onConfirm(); 
    }, 2000);
  };

  if (confirmed) {
    return (
      <Modal onClose={onClose} title="Contribution Successful!">
        <div className="text-center py-6">
          <CheckCircle size={48} className="text-green-400 mx-auto mb-4"/>
          <p className="text-slate-200 text-lg">Your contribution of {details.amount} (mock ETH/DAI) to "{details.to}" was successful.</p>
          <a href="#" className="text-sky-400 hover:underline text-sm mt-2 block" target="_blank" rel="noopener noreferrer">View Transaction on Etherscan (Mock) <ExternalLink size={14} className="inline"/></a>
          <Button onClick={onClose} className="mt-6">Close</Button>
        </div>
      </Modal>
    );
  }

  if (processing) {
     return (
      <Modal onClose={() => {}} title="Processing Contribution..."> 
        <div className="text-center py-10">
          <div className="animate-spin rounded-full h-12 w-12 border-b-2 border-sky-400 mx-auto mb-4"></div>
          <p className="text-slate-300">Processing your transaction...</p>
          <p className="text-xs text-slate-500 mt-1">Please wait, this may take a moment.</p>
        </div>
      </Modal>
    );
  }

  return (
    <Modal onClose={onClose} title="Confirm Your Contribution">
      <div className="space-y-3 text-slate-300">
        <p><strong>To:</strong> <span className="text-sky-400">{details.to}</span> ({details.type})</p>
        <p><strong>Amount:</strong> <span className="text-sky-400">{details.amount}</span> (mock ETH/DAI)</p>
        <p><strong>Estimated Gas Fee (mock):</strong> {mockGasFee} ETH</p>
        <hr className="border-slate-700"/>
        <p><strong>Total (mock):</strong> {(parseFloat(details.amount) + mockGasFee).toFixed(4)} ETH</p>
      </div>
      <div className="mt-6 flex justify-end space-x-3">
        <Button onClick={onClose} variant="secondary">Reject</Button>
        <Button onClick={handleConfirmClick}>Confirm</Button>
      </div>
    </Modal>
  );
};


// --- Admin Pages ---
const AdminLayout = ({ children }) => {
  const { isAdmin } = useContext(AppContext);
  if (!isAdmin) return <AdminLoginPage />; 

  return (
    <div className="flex min-h-[calc(100vh-4rem)]"> 
      <AdminSidebar />
      <div className="flex-grow p-4 md:p-8"> 
        {children}
      </div>
    </div>
  );
};

const AdminLoginPage = () => {
  const { handleAdminLogin, navigate } = useContext(AppContext);
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');

  const handleSubmit = (e) => {
    e.preventDefault();
    if (email === 'admin@cascadeqf.io' && password === 'password') {
      handleAdminLogin(true);
    } else {
      alert('Invalid admin credentials (mock). Use admin@cascadeqf.io / password');
      handleAdminLogin(false);
    }
  };

  return (
    <div className="flex items-center justify-center min-h-[calc(100vh-8rem)]">
      <Card className="w-full max-w-md">
        <h2 className="text-3xl font-bold text-sky-400 mb-6 text-center">Admin Login</h2>
        <form onSubmit={handleSubmit} className="space-y-4">
          <Input label="Email" id="adminEmail" type="email" value={email} onChange={(e) => setEmail(e.target.value)} placeholder="admin@cascadeqf.io" required />
          <Input label="Password" id="adminPassword" type="password" value={password} onChange={(e) => setPassword(e.target.value)} placeholder="••••••••" required />
          <Button type="submit" className="w-full" iconLeft={<LogIn size={18}/>}>Login</Button>
        </form>
        <p className="text-xs text-slate-500 mt-4 text-center">
            (For mockup, use: admin@cascadeqf.io / password)
        </p>
        <div className="mt-6 text-center">
            <Button onClick={() => navigate('home')} variant="secondary" size="sm">Back to Public Site</Button>
        </div>
      </Card>
    </div>
  );
};

const AdminDashboardPage = () => {
  const { cascades, pools, navigate } = useContext(AppContext); // Renamed
  const totalTVL = cascades.reduce((sum, cascade) => sum + cascade.totalContributed, 0); // Renamed
  const totalContributors = cascades.reduce((sum, cascade) => sum + cascade.contributors, 0); // Renamed

  const stats = [
    { label: 'Total Value in Cascades', value: `$${totalTVL.toLocaleString()}`, icon: <DollarSign size={24} className="text-sky-500"/> }, // Renamed
    { label: 'Active Cascades', value: cascades.filter(c => c.status.includes('Active')).length, icon: <Database size={24} className="text-green-500"/> }, // Renamed
    { label: 'Active Pools', value: pools.filter(p => p.status.includes('Open') || p.status.includes('Accepting')).length, icon: <GitFork size={24} className="text-yellow-500"/> }, // Renamed
    { label: 'Total Cascade Contributors', value: totalContributors, icon: <Users size={24} className="text-indigo-500"/> }, // Renamed
  ];

  return (
    <AdminLayout>
      <h2 className="text-3xl font-bold text-sky-400 mb-8">Admin Dashboard</h2>
      <div className="grid md:grid-cols-2 lg:grid-cols-4 gap-6 mb-8">
        {stats.map(stat => (
          <Card key={stat.label} className="flex items-center space-x-4">
            {stat.icon}
            <div>
              <p className="text-2xl font-semibold text-slate-100">{stat.value}</p>
              <p className="text-sm text-slate-400">{stat.label}</p>
            </div>
          </Card>
        ))}
      </div>
      <div className="mb-8">
        <h3 className="text-2xl font-semibold text-sky-400 mb-4">Quick Actions</h3>
        <div className="flex space-x-4">
          <Button onClick={() => navigate('adminCreateCascade')} iconLeft={<PlusCircle size={18}/>}>Create New Cascade</Button> {/* Renamed */}
          <Button onClick={() => navigate('adminCreatePool')} variant="secondary" iconLeft={<PlusCircle size={18}/>}>Create Independent Pool</Button> {/* Renamed */}
        </div>
      </div>
      <Card>
        <h3 className="text-2xl font-semibold text-sky-400 mb-4">Recent Activity (Mock)</h3>
        <ul className="space-y-2 text-sm text-slate-300">
          <li><span className="text-green-400">[NEW CASCADE]</span> "Sustainable Urban Development Cascade" created.</li> {/* Renamed */}
          <li><span className="text-yellow-400">[ALLOCATION]</span> "Clean Environment Cascade" allocated $5,000 to "Water Purity Pool".</li> {/* Renamed */}
          <li><span className="text-blue-400">[CONTRIBUTION]</span> Large contribution of $1,000 to "Open Source Tech Cascade".</li> {/* Renamed */}
        </ul>
      </Card>
    </AdminLayout>
  );
};

const AdminManageCascadesPage = () => { // Renamed
  const { cascades, navigate, setCurrentAdminCascadeId, setCascades } = useContext(AppContext); // Renamed

  const handleDeleteCascade = (cascadeId) => { // Renamed
    if (window.confirm("Are you sure you want to delete this Cascade? This action cannot be undone and might affect linked Pools.")) { // Renamed
        setCascades(prev => prev.filter(c => c.id !== cascadeId)); // Renamed
    }
  };

  return (
    <AdminLayout>
      <div className="flex justify-between items-center mb-6">
        <h2 className="text-3xl font-bold text-sky-400">Manage Cascades</h2> {/* Renamed */}
        <Button onClick={() => navigate('adminCreateCascade')} iconLeft={<PlusCircle size={18}/>}>Create New Cascade</Button> {/* Renamed */}
      </div>
      <Card className="overflow-x-auto">
        {cascades.length > 0 ? ( // Renamed
          <table className="w-full min-w-[600px] text-sm text-left text-slate-300">
            <thead className="text-xs text-sky-400 uppercase bg-slate-700">
              <tr>
                <th scope="col" className="px-4 py-3">Name</th>
                <th scope="col" className="px-4 py-3">Status</th>
                <th scope="col" className="px-4 py-3">Total in Cascade</th> {/* Renamed */}
                <th scope="col" className="px-4 py-3">Contributors</th>
                <th scope="col" className="px-4 py-3">Actions</th>
              </tr>
            </thead>
            <tbody>
              {cascades.map(cascade => ( // Renamed
                <tr key={cascade.id} className="bg-slate-800 border-b border-slate-700 hover:bg-slate-750">
                  <td className="px-4 py-3 font-medium text-slate-100">{cascade.name}</td>
                  <td className="px-4 py-3"><span className={`px-2 py-0.5 rounded-full text-xs font-semibold ${cascade.status.includes('Active') ? 'bg-green-700 text-green-200' : 'bg-yellow-700 text-yellow-200'}`}>{cascade.status}</span></td>
                  <td className="px-4 py-3">${cascade.totalContributed.toLocaleString()}</td>
                  <td className="px-4 py-3">{cascade.contributors}</td>
                  <td className="px-4 py-3 flex space-x-1 sm:space-x-2">
                    <Button onClick={() => { setCurrentAdminCascadeId(cascade.id); navigate(`adminEditCascade/${cascade.id}`); }} variant="outline" size="sm" className="!p-1.5" title="Edit"><Edit3 size={14}/></Button> {/* Renamed */}
                    <Button 
                        onClick={() => { setCurrentAdminCascadeId(cascade.id); navigate(`adminInitiateAllocation/${cascade.id}`); }} 
                        variant="secondary" 
                        size="sm" 
                        className="!p-1.5" 
                        title="Allocate Funds to New Pools" // Renamed
                        disabled={cascade.status === 'Allocation Pending' || cascade.totalContributed <= 0}
                    >
                        <GitFork size={14}/>
                    </Button>
                    <Button onClick={() => handleDeleteCascade(cascade.id)} variant="danger" size="sm" className="!p-1.5" title="Delete"><Trash2 size={14}/></Button> {/* Renamed */}
                  </td>
                </tr>
              ))}
            </tbody>
          </table>
        ) : (
          <p className="text-slate-400 text-center py-6">No Cascades created yet.</p> // Renamed
        )}
      </Card>
    </AdminLayout>
  );
};

const AdminCreateEditCascadePage = ({ editMode = false, cascadeId }) => { // Renamed
  const { cascades, setCascades, navigate } = useContext(AppContext); // Renamed
  const [cascadeData, setCascadeData] = useState({ // Renamed
    id: editMode && cascadeId ? cascadeId : `c${Date.now()}`, // Renamed prefix
    name: '',
    description: '',
    status: 'Draft',
    defaultQFParams: {
      allocationMechanism: 'Quadratic Funding based on Proposals',
      minContributionProposal: 10,
      matchingCapPool: 5000, // Renamed from matchingCapSubPool
      qfCoefficient: 1.0,
    },
    fundedPools: [], // Renamed
    totalContributed: 0, 
    contributors: 0, 
  });

  useEffect(() => {
    if (editMode && cascadeId) {
      const existingCascade = cascades.find(c => c.id === cascadeId); // Renamed
      if (existingCascade) {
        setCascadeData(existingCascade); // Renamed
      } else {
        navigate('adminManageCascades'); // Renamed
      }
    }
  }, [editMode, cascadeId, cascades, navigate]); // Renamed

  const handleChange = (e) => {
    const { name, value } = e.target;
    setCascadeData(prev => ({ ...prev, [name]: value })); // Renamed
  };

  const handleQFParamChange = (e) => {
    const { name, value } = e.target;
    setCascadeData(prev => ({ // Renamed
      ...prev,
      defaultQFParams: { ...prev.defaultQFParams, [name]: name === 'allocationMechanism' ? value : parseFloat(value) || 0 }
    }));
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    if (editMode) {
      setCascades(prev => prev.map(c => c.id === cascadeId ? cascadeData : c)); // Renamed
    } else {
      const newCascade = { ...cascadeData, totalContributed: cascadeData.totalContributed || 0, contributors: cascadeData.contributors || 0, fundedPools: cascadeData.fundedPools || [] }; // Renamed
      setCascades(prev => [...prev, newCascade]); // Renamed
    }
    navigate('adminManageCascades'); // Renamed
  };

  return (
    <AdminLayout>
      <h2 className="text-3xl font-bold text-sky-400 mb-6">{editMode ? `Edit Cascade: ${cascadeData.name}` : 'Create New Cascade'}</h2> {/* Renamed */}
      <Card>
        <form onSubmit={handleSubmit} className="space-y-6">
          <Input label="Cascade Name" id="name" name="name" value={cascadeData.name} onChange={handleChange} required /> {/* Renamed */}
          <Textarea label="Description (Purpose of this Cascade)" id="description" name="description" value={cascadeData.description} onChange={handleChange} rows={4} required placeholder="e.g., To fund initiatives related to environmental conservation through various Pools."/> {/* Renamed */}
          <Select label="Status" id="status" name="status" value={cascadeData.status} onChange={handleChange}>
            <option value="Draft">Draft</option>
            <option value="Active - Accepting Contributions">Active - Accepting Contributions</option>
            <option value="Allocation Pending">Allocation Pending (Awaiting admin action to allocate funds)</option>
            <option value="Closed">Closed (No longer active)</option>
          </Select>

          <fieldset className="border border-slate-600 p-4 rounded-md">
            <legend className="text-lg font-semibold text-sky-400 px-2">Default Mechanism for Allocating Funds to Pools</legend> {/* Renamed */}
            <p className="text-xs text-slate-400 mb-3">This determines how funds from THIS Cascade are distributed to create and seed new Pools.</p> {/* Renamed */}
            <Select label="Allocation Mechanism" id="allocationMechanism" name="allocationMechanism" value={cascadeData.defaultQFParams.allocationMechanism} onChange={handleQFParamChange}>
              <option value="Quadratic Funding based on Proposals">Quadratic Funding (based on community proposals for Pools)</option> {/* Renamed */}
              <option value="Manual Allocation">Manual Allocation (Admin decides amounts for each new Pool)</option> {/* Renamed */}
              <option value="Equal Distribution">Equal Distribution (Divide funds equally among defined new Pools)</option> {/* Renamed */}
            </Select>
            {cascadeData.defaultQFParams.allocationMechanism === 'Quadratic Funding based on Proposals' && (
              <>
                <Input label="Min Contribution for Pool Proposal (mock units)" id="minContributionProposal" name="minContributionProposal" type="number" value={cascadeData.defaultQFParams.minContributionProposal} onChange={handleQFParamChange} /> {/* Renamed */}
                <Input label="Matching Cap per resulting Pool (mock units)" id="matchingCapPool" name="matchingCapPool" type="number" value={cascadeData.defaultQFParams.matchingCapPool} onChange={handleQFParamChange} /> {/* Renamed */}
                <Input label="QF Coefficient for Pool Proposals (e.g., 1.0, 1.5)" id="qfCoefficient" name="qfCoefficient" type="number" step="0.1" value={cascadeData.defaultQFParams.qfCoefficient} onChange={handleQFParamChange} /> {/* Renamed */}
              </>
            )}
          </fieldset>

          <div className="flex justify-end space-x-3 pt-4">
            <Button type="button" variant="secondary" onClick={() => navigate('adminManageCascades')}>Cancel</Button> {/* Renamed */}
            <Button type="submit">{editMode ? 'Save Changes' : 'Create Cascade'}</Button> {/* Renamed */}
          </div>
        </form>
      </Card>
    </AdminLayout>
  );
};

const AdminInitiateAllocationPage = ({ cascadeId }) => { // Renamed
  const { cascades, setCascades, pools, setPools, navigate } = useContext(AppContext); // Renamed
  const cascade = cascades.find(c => c.id === cascadeId); // Renamed

  const [allocationAmount, setAllocationAmount] = useState(cascade ? Math.floor(cascade.totalContributed * 0.8) : 0); 
  const [definedPools, setDefinedPools] = useState([]); // Renamed
  const [newPoolName, setNewPoolName] = useState(''); // Renamed
  const [newPoolDesc, setNewPoolDesc] = useState(''); // Renamed
  const [newPoolTarget, setNewPoolTarget] = useState(0); // Renamed

  const mockPoolProposals = [ // Renamed
    { id: 'prop1', title: 'Renewable Energy Research Pool', proposer: 'CommunityDAO', requestedAmount: cascade ? Math.floor(cascade.totalContributed * 0.3) : 1500, votes: 25 }, // Renamed
    { id: 'prop2', title: 'Local Food Security Pool', proposer: 'EcoGroup', requestedAmount: cascade ? Math.floor(cascade.totalContributed * 0.2) : 1000, votes: 18 }, // Renamed
    { id: 'prop3', title: 'Youth Tech Education Pool', proposer: 'EduFuture', requestedAmount: cascade ? Math.floor(cascade.totalContributed * 0.25) : 1200, votes: 22 }, // Renamed
  ];
  const [selectedProposals, setSelectedProposals] = useState([]);

  useEffect(() => { 
    if (cascade) {
        setAllocationAmount(Math.floor(cascade.totalContributed * 0.8));
    }
  }, [cascade?.totalContributed, cascade]); 

  if (!cascade) {
    return <AdminLayout><p className="text-red-400">Cascade not found.</p></AdminLayout>; // Renamed
  }

  const handleAddManualPool = () => { // Renamed
    if (!newPoolName.trim() || (cascade.defaultQFParams.allocationMechanism === 'Manual Allocation' && newPoolTarget <= 0) ) {
        alert("Please provide a name for the new Pool. If using Manual Allocation, also set a target allocation greater than zero."); // Renamed
        return;
    }
    setDefinedPools([...definedPools, { id: `pool_manual_${Date.now()}`, name: newPoolName, description: newPoolDesc, targetAllocation: newPoolTarget }]); // Renamed
    setNewPoolName('');
    setNewPoolDesc('');
    setNewPoolTarget(0);
  };

  const handleExecuteAllocation = () => {
    let poolsToFundData = []; // Renamed
    let totalRequestedOrTargeted = 0;

    if (cascade.defaultQFParams.allocationMechanism === 'Quadratic Funding based on Proposals') {
        if (selectedProposals.length === 0) {
            alert("Please select at least one proposal to fund.");
            return;
        }
        const proposalsToFund = mockPoolProposals.filter(p => selectedProposals.includes(p.id)); // Renamed
        const totalVotes = proposalsToFund.reduce((sum, p) => sum + p.votes, 0);
        
        if (totalVotes === 0) { 
            alert("Selected proposals have no collective votes. Cannot allocate via QF.");
            return;
        }

        poolsToFundData = proposalsToFund.map(proposal => { // Renamed
            const fundForThisProposal = Math.min(proposal.requestedAmount, Math.floor((proposal.votes / totalVotes) * allocationAmount));
            return { 
                name: proposal.title, 
                description: `Pool funded based on community proposal by ${proposal.proposer}. Seeded by ${cascade.name}.`, // Renamed
                matchingFund: fundForThisProposal
            };
        });
        totalRequestedOrTargeted = poolsToFundData.reduce((sum, p) => sum + p.matchingFund, 0); // Renamed

    } else if (cascade.defaultQFParams.allocationMechanism === 'Manual Allocation') {
        if (definedPools.length === 0) {
            alert("Please define at least one Pool for manual allocation."); // Renamed
            return;
        }
        poolsToFundData = definedPools.map(p_def => ({ // Renamed
            name: p_def.name,
            description: p_def.description || `Pool manually defined. Seeded by ${cascade.name}.`, // Renamed
            matchingFund: p_def.targetAllocation 
        }));
        totalRequestedOrTargeted = poolsToFundData.reduce((sum, p) => sum + p.matchingFund, 0); // Renamed
        if (totalRequestedOrTargeted > allocationAmount) {
            alert(`Error: Total target allocation for defined Pools ($${totalRequestedOrTargeted.toLocaleString()}) exceeds the amount available for allocation from this Cascade ($${allocationAmount.toLocaleString()}). Please adjust targets or the total allocation amount.`); // Renamed
            return;
        }
    } else { // Equal Distribution
        if (definedPools.length === 0) {
            alert("Please define at least one Pool for equal distribution."); // Renamed
            return;
        }
        const fundPerPool = Math.floor(allocationAmount / definedPools.length);
        poolsToFundData = definedPools.map(p_def => ({ // Renamed
            name: p_def.name,
            description: p_def.description || `Pool funded via equal distribution. Seeded by ${cascade.name}.`, // Renamed
            matchingFund: fundPerPool
        }));
        totalRequestedOrTargeted = poolsToFundData.reduce((sum, p) => sum + p.matchingFund, 0); // Renamed
    }
    
    const finalTotalAllocated = Math.min(totalRequestedOrTargeted, allocationAmount);

    const newPoolsToAdd = poolsToFundData.map(p_data => ({ // Renamed
      id: `pool${Date.now()}_${Math.random().toString(36).substr(2, 5)}`, // Renamed prefix
      parentCascadeId: cascade.id, // Renamed
      name: p_data.name,
      description: p_data.description,
      matchingFund: p_data.matchingFund, 
      grantStreamCount: 0, // Renamed
      contributors: 0,
      status: 'Accepting Grant Stream Applications', // Renamed
      qfParams: { 
        matchingMultiplier: 1.0, capPerContributor: 50, maxQFMatchPerGrantStream: 500, minUniqueContributors: 3, minDirectFunding: 20 // Renamed
      }
    }));

    setPools(prev => [...prev, ...newPoolsToAdd]); // Renamed
    setCascades(prev => prev.map(c => c.id === cascadeId ? { // Renamed
        ...c, 
        totalContributed: c.totalContributed - finalTotalAllocated, 
        status: c.totalContributed - finalTotalAllocated > 0 ? 'Active - Accepting Contributions' : 'Closed', 
        fundedPools: [ // Renamed
            ...(c.fundedPools || []).filter(fp => !newPoolsToAdd.find(np => np.id === fp.id)), 
            ...newPoolsToAdd.map(np => ({id: np.id, name: np.name, allocated: np.matchingFund}))
        ]
    } : c)); 
    
    alert(`Allocation Complete! ${newPoolsToAdd.length} new Pools have been created and seeded with a total of $${finalTotalAllocated.toLocaleString()} from ${cascade.name}.`); // Renamed
    navigate('adminManageCascades'); // Renamed
  };

  return (
    <AdminLayout>
      <h2 className="text-3xl font-bold text-sky-400 mb-2">Allocate Funds from Cascade: <span className="text-sky-500">{cascade.name}</span></h2> {/* Renamed */}
      <p className="text-slate-400 mb-1">Current Total Funds in Cascade: <strong className="text-emerald-400">${cascade.totalContributed.toLocaleString()}</strong></p> {/* Renamed */}
      <p className="text-xs text-slate-500 mb-1">(Note: This amount is dynamic. New contributions to this Cascade before allocation will increase the available funds.)</p> {/* Renamed */}
      <p className="text-xs text-slate-500 mb-6">This process will use funds from <strong className="text-sky-300">{cascade.name}</strong> to create and provide initial matching funds for new Pools.</p> {/* Renamed */}
      
      <Card className="space-y-6">
        <fieldset className="border border-slate-600 p-4 rounded-md">
          <legend className="text-lg font-semibold text-sky-400 px-2">Step 1: Configure Allocation Amount</legend>
          <Input 
            label={`Total Amount from "${cascade.name}" to Allocate for New Pools`} // Renamed
            type="number" 
            value={allocationAmount} 
            onChange={e => setAllocationAmount(parseFloat(e.target.value) || 0)} 
            max={cascade.totalContributed}
            helperText={`Max available from Cascade: $${cascade.totalContributed.toLocaleString()}`} // Renamed
          />
           <p className="text-xs text-slate-400 mt-1">This amount will be drawn from the Cascade to seed the matching funds of the new Pools defined below.</p> {/* Renamed */}
        </fieldset>

        <fieldset className="border border-slate-600 p-4 rounded-md">
          <legend className="text-lg font-semibold text-sky-400 px-2">Step 2: Define New Pools to be Funded</legend> {/* Renamed */}
          <p className="text-sm text-slate-400 mb-3">Cascade's Default Allocation Mechanism: <strong className="text-sky-300">{cascade.defaultQFParams.allocationMechanism.replace('Sub-Pool', 'Pool').replace('Grant Round', 'Pool')}</strong></p> {/* Renamed */}

          {cascade.defaultQFParams.allocationMechanism === 'Quadratic Funding based on Proposals' && (
            <div>
              <h4 className="font-semibold text-slate-200 mb-2">Select Pool Proposals for Allocation:</h4> {/* Renamed */}
              {mockPoolProposals.map(prop => ( // Renamed
                <div key={prop.id} className="flex items-center space-x-2 mb-1 p-2 bg-slate-700 rounded">
                  <input 
                    type="checkbox" 
                    id={`prop_${prop.id}`} 
                    checked={selectedProposals.includes(prop.id)}
                    onChange={(e) => {
                      if (e.target.checked) setSelectedProposals([...selectedProposals, prop.id]);
                      else setSelectedProposals(selectedProposals.filter(id => id !== prop.id));
                    }}
                    className="form-checkbox h-4 w-4 text-sky-500 bg-slate-600 border-slate-500 rounded focus:ring-sky-400"
                  />
                  <label htmlFor={`prop_${prop.id}`} className="text-sm text-slate-300">
                    {prop.title} (Requested: ${prop.requestedAmount.toLocaleString()}, Votes: {prop.votes})
                  </label>
                </div>
              ))}
              <p className="text-xs text-slate-400 mt-2">The selected proposals will receive a share of the total allocation amount based on their QF score (mocked by votes here), up to their requested amount.</p>
            </div>
          )}

          { (cascade.defaultQFParams.allocationMechanism === 'Manual Allocation' || cascade.defaultQFParams.allocationMechanism === 'Equal Distribution') && (
            <div>
              <h4 className="font-semibold text-slate-200 mb-2">Manually Define New Pools:</h4> {/* Renamed */}
              <div className="space-y-3 p-3 bg-slate-700 rounded-md mb-4">
                <Input label="New Pool Name" value={newPoolName} onChange={e => setNewPoolName(e.target.value)} placeholder="e.g., Water Quality Pool Q4"/> {/* Renamed */}
                <Textarea label="New Pool Description" value={newPoolDesc} onChange={e => setNewPoolDesc(e.target.value)} placeholder="Brief description of this Pool's focus" rows={2}/> {/* Renamed */}
                {cascade.defaultQFParams.allocationMechanism === 'Manual Allocation' &&
                    <Input label="Target Matching Fund for this Pool (from Cascade)" type="number" value={newPoolTarget} onChange={e => setNewPoolTarget(parseFloat(e.target.value) || 0)} /> // Renamed
                }
                <Button onClick={handleAddManualPool} variant="secondary" iconLeft={<PlusCircle size={16}/>}>Add Pool Definition</Button> {/* Renamed */}
              </div>
              {definedPools.length > 0 && (
                <div>
                  <h5 className="font-medium text-slate-300 mb-1">Defined Pools for this Allocation:</h5> {/* Renamed */}
                  <ul className="list-disc list-inside text-sm text-slate-400 space-y-1">
                    {definedPools.map(p_def => <li key={p_def.id}>{p_def.name} {cascade.defaultQFParams.allocationMechanism === 'Manual Allocation' ? `(Target Matching Fund: $${p_def.targetAllocation.toLocaleString()})` : '(Will receive equal share)'}</li>)} {/* Renamed */}
                  </ul>
                </div>
              )}
               <p className="text-xs text-slate-400 mt-2">
                {cascade.defaultQFParams.allocationMechanism === 'Manual Allocation' ? 'Each defined Pool will be seeded with its target matching fund from the Cascade.' : 'The total allocation amount will be divided equally among the defined Pools to seed their matching funds from the Cascade.'} {/* Renamed */}
               </p>
            </div>
          )}
        </fieldset>

        <fieldset className="border border-slate-600 p-4 rounded-md">
          <legend className="text-lg font-semibold text-sky-400 px-2">Step 3: Review & Execute Allocation</legend>
          <div className="bg-slate-700 p-3 rounded mb-4">
            <h4 className="font-semibold text-slate-200">Allocation Preview:</h4>
            <p className="text-sm text-slate-300">Cascade "<strong className="text-sky-300">{cascade.name}</strong>" will allocate <strong className="text-emerald-400">${allocationAmount.toLocaleString()}</strong>.</p> {/* Renamed */}
            <p className="text-sm text-slate-300">This amount will be used to create and seed the matching funds for the following new Pools:</p> {/* Renamed */}
            <ul className="list-disc list-inside text-xs text-slate-400 pl-4 mt-1">
              {(cascade.defaultQFParams.allocationMechanism === 'Quadratic Funding based on Proposals' ? 
                selectedProposals.map(id => {
                    const proposal = mockPoolProposals.find(p=>p.id===id); // Renamed
                    const proposalsToFund = mockPoolProposals.filter(p => selectedProposals.includes(p.id)); // Renamed
                    const totalVotes = proposalsToFund.reduce((sum, p) => sum + p.votes, 0);
                    const fundForThisProposal = totalVotes > 0 ? Math.min(proposal.requestedAmount, Math.floor((proposal.votes / totalVotes) * allocationAmount)) : 0;
                    return `${proposal?.title} (Est. Matching Fund: $${fundForThisProposal.toLocaleString()})`;
                }) : 
                definedPools.map(p_def => { // Renamed
                    const fund = cascade.defaultQFParams.allocationMechanism === 'Manual Allocation' ? p_def.targetAllocation : (definedPools.length > 0 ? Math.floor(allocationAmount/definedPools.length) : 0);
                    return `${p_def.name} (Est. Matching Fund: $${fund.toLocaleString()})`;
                })
              ).filter(Boolean).map(name => <li key={name}>{name}</li>)}
            </ul>
             <p className="text-xs text-slate-500 mt-2">The actual matching fund for each new Pool will be determined by the chosen allocation mechanism and the total amount allocated from the Cascade.</p> {/* Renamed */}
          </div>
          <div className="flex justify-end space-x-3">
            <Button type="button" variant="secondary" onClick={() => navigate('adminManageCascades')}>Cancel</Button> {/* Renamed */}
            <Button onClick={handleExecuteAllocation} iconLeft={<CheckCircle size={18}/>} disabled={allocationAmount <=0 || (cascade.defaultQFParams.allocationMechanism === 'Quadratic Funding based on Proposals' && selectedProposals.length === 0) || (cascade.defaultQFParams.allocationMechanism !== 'Quadratic Funding based on Proposals' && definedPools.length === 0) }>Execute Allocation</Button>
          </div>
        </fieldset>
      </Card>
    </AdminLayout>
  );
};

const AdminManagePoolsPage = () => { // Renamed
    const { pools, navigate, setPools, setCurrentAdminPoolId, cascades } = useContext(AppContext); // Renamed

    const handleDeletePool = (poolId) => { // Renamed
        if (window.confirm("Are you sure you want to delete this Pool? This action cannot be undone.")) { // Renamed
            setPools(prev => prev.filter(p => p.id !== poolId)); // Renamed
        }
    };

    return (
        <AdminLayout>
            <div className="flex justify-between items-center mb-6">
                <h2 className="text-3xl font-bold text-sky-400">Manage Pools</h2> {/* Renamed */}
                <Button onClick={() => navigate('adminCreatePool')} iconLeft={<PlusCircle size={18}/>}>Create Independent Pool</Button> {/* Renamed */}
            </div>
            <Card className="overflow-x-auto">
                {pools.length > 0 ? ( // Renamed
                    <table className="w-full min-w-[700px] text-sm text-left text-slate-300">
                        <thead className="text-xs text-sky-400 uppercase bg-slate-700">
                            <tr>
                                <th scope="col" className="px-4 py-3">Name</th>
                                <th scope="col" className="px-4 py-3">Parent Cascade</th> {/* Renamed */}
                                <th scope="col" className="px-4 py-3">Status</th>
                                <th scope="col" className="px-4 py-3">Matching Fund</th>
                                <th scope="col" className="px-4 py-3">Grant Streams</th> {/* Renamed */}
                                <th scope="col" className="px-4 py-3">Actions</th>
                            </tr>
                        </thead>
                        <tbody>
                            {pools.map(pool => { // Renamed
                                const parentCascade = cascades.find(c => c.id === pool.parentCascadeId); // Renamed
                                return (
                                <tr key={pool.id} className="bg-slate-800 border-b border-slate-700 hover:bg-slate-750">
                                    <td className="px-4 py-3 font-medium text-slate-100">{pool.name}</td>
                                    <td className="px-4 py-3 text-xs">{parentCascade ? parentCascade.name : 'N/A (Independent)'}</td> {/* Renamed */}
                                    <td className="px-4 py-3"><span className={`px-2 py-0.5 rounded-full text-xs font-semibold ${pool.status.includes('Open') || pool.status.includes('Accepting') ? 'bg-green-700 text-green-200' : 'bg-yellow-700 text-yellow-200'}`}>{pool.status}</span></td>
                                    <td className="px-4 py-3">${pool.matchingFund.toLocaleString()}</td>
                                    <td className="px-4 py-3">{pool.grantStreamCount}</td> {/* Renamed */}
                                    <td className="px-4 py-3 flex space-x-1 sm:space-x-2">
                                        <Button onClick={() => { setCurrentAdminPoolId(pool.id); navigate(`adminEditPool/${pool.id}`); }} variant="outline" size="sm" className="!p-1.5" title="Edit"><Edit3 size={14}/></Button> {/* Renamed */}
                                        <Button onClick={() => handleDeletePool(pool.id)} variant="danger" size="sm" className="!p-1.5" title="Delete"><Trash2 size={14}/></Button> {/* Renamed */}
                                    </td>
                                </tr>
                            )})}
                        </tbody>
                    </table>
                ) : (
                    <p className="text-slate-400 text-center py-6">No Pools created yet.</p> // Renamed
                )}
            </Card>
        </AdminLayout>
    );
};

const AdminCreateEditPoolPage = ({ editMode = false, poolId, parentCascadeIdParam = null }) => { // Renamed
    const { pools, setPools, cascades, navigate } = useContext(AppContext); // Renamed
    const [poolData, setPoolData] = useState({ // Renamed
        id: editMode && poolId ? poolId : `p${Date.now()}`, // Renamed prefix
        name: '',
        description: '',
        parentCascadeId: parentCascadeIdParam || '', // Renamed
        initialMatchingFund: 0,
        status: 'Draft',
        timelines: { appStartDate: '', appEndDate: '', contribStartDate: '', contribEndDate: '', matchingDate: '' },
        eligibilityCriteria: '',
        qfParams: { 
            matchingMultiplier: 1.0, 
            capPerContributor: 50, 
            maxQFMatchPerGrantStream: 1000, // Renamed
            minUniqueContributors: 3, 
            minDirectFunding: 50 
        },
        grantStreamCount: 0, // Renamed
        contributors: 0, 
    });

    useEffect(() => {
        if (editMode && poolId) {
            const existingPool = pools.find(p => p.id === poolId); // Renamed
            if (existingPool) {
                setPoolData(existingPool); // Renamed
            } else {
                navigate('adminManagePools'); // Renamed
            }
        } else if (parentCascadeIdParam) { 
            setPoolData(prev => ({ ...prev, parentCascadeId: parentCascadeIdParam })); // Renamed
        }
    }, [editMode, poolId, pools, navigate, parentCascadeIdParam]); // Renamed

    const handleChange = (e) => {
        const { name, value, type } = e.target;
        setPoolData(prev => ({ ...prev, [name]: type === 'number' ? parseFloat(value) || 0 : value })); // Renamed
    };

    const handleTimelineChange = (e) => {
        const { name, value } = e.target;
        setPoolData(prev => ({ ...prev, timelines: { ...prev.timelines, [name]: value }})); // Renamed
    };
    
    const handleQFParamChange = (e) => {
        const { name, value } = e.target;
        setPoolData(prev => ({ ...prev, qfParams: { ...prev.qfParams, [name]: parseFloat(value) || 0 }})); // Renamed
    };

    const handleSubmit = (e) => {
        e.preventDefault();
        if (editMode) {
            setPools(prev => prev.map(p => p.id === poolId ? poolData : p)); // Renamed
        } else {
            const newPool = { ...poolData, grantStreamCount: 0, contributors: 0 }; // Renamed
            setPools(prev => [...prev, newPool]); // Renamed
        }
        navigate('adminManagePools'); // Renamed
    };
    
    const parentCascadeName = cascades.find(c => c.id === poolData.parentCascadeId)?.name; // Renamed

    return (
        <AdminLayout>
            <h2 className="text-3xl font-bold text-sky-400 mb-6">{editMode ? `Edit Pool: ${poolData.name}` : 'Create New Pool'}</h2> {/* Renamed */}
            {parentCascadeName && !editMode && <p className="text-sky-300 mb-4 text-sm">This Pool will be seeded by the "{parentCascadeName}" Cascade.</p>} {/* Renamed */}
            <Card>
                <form onSubmit={handleSubmit} className="space-y-6">
                    <Input label="Pool Name" name="name" value={poolData.name} onChange={handleChange} required /> {/* Renamed */}
                    <Textarea label="Description" name="description" value={poolData.description} onChange={handleChange} rows={3} required placeholder="e.g., Funding innovative Grant Streams in reforestation and biodiversity."/> {/* Renamed */}
                    
                    <Select 
                        label="Parent Cascade (If this Pool is seeded by a Cascade)" // Renamed
                        name="parentCascadeId" // Renamed
                        value={poolData.parentCascadeId} 
                        onChange={handleChange}
                        disabled={editMode && poolData.parentCascadeId} 
                    >
                        <option value="">N/A (This is an Independent Pool)</option> {/* Renamed */}
                        {cascades.map(c => <option key={c.id} value={c.id}>{c.name}</option>)} {/* Renamed */}
                    </Select>
                    <Input label="Initial Matching Fund for this Pool" name="initialMatchingFund" type="number" value={poolData.initialMatchingFund} onChange={handleChange} required 
                           helperText={poolData.parentCascadeId ? "This amount is typically set during Cascade allocation." : "Enter the total matching amount for this independent Pool."} // Renamed
                    />
                    <Select label="Status" name="status" value={poolData.status} onChange={handleChange}>
                        <option value="Draft">Draft (Not visible to public)</option>
                        <option value="Accepting Grant Stream Applications">Accepting Grant Stream Applications</option> {/* Renamed */}
                        <option value="Open for Contributions">Open for Contributions (Grant Streams can receive funds)</option> {/* Renamed */}
                        <option value="Matching in Progress">Matching in Progress (Contributions closed, calculating matches)</option>
                        <option value="Closed - Funds Distributed">Closed - Funds Distributed</option>
                        <option value="Closed">Closed (Archived)</option>
                    </Select>

                    <fieldset className="border border-slate-600 p-4 rounded-md">
                        <legend className="text-lg font-semibold text-sky-400 px-2">Timelines</legend>
                        <div className="grid md:grid-cols-2 gap-4">
                            <Input label="Grant Stream Application Start Date" name="appStartDate" type="date" value={poolData.timelines.appStartDate} onChange={handleTimelineChange} /> {/* Renamed */}
                            <Input label="Grant Stream Application End Date" name="appEndDate" type="date" value={poolData.timelines.appEndDate} onChange={handleTimelineChange} /> {/* Renamed */}
                            <Input label="Contribution Period Start Date" name="contribStartDate" type="date" value={poolData.timelines.contribStartDate} onChange={handleTimelineChange} />
                            <Input label="Contribution Period End Date" name="contribEndDate" type="date" value={poolData.timelines.contribEndDate} onChange={handleTimelineChange} />
                            <Input label="Matching Calculation Date" name="matchingDate" type="date" value={poolData.timelines.matchingDate} onChange={handleTimelineChange} />
                        </div>
                    </fieldset>

                    <Textarea label="Eligibility Criteria for Grant Streams" name="eligibilityCriteria" value={poolData.eligibilityCriteria} onChange={handleChange} rows={3} placeholder="Detail the requirements for Grant Streams to apply to this Pool."/> {/* Renamed */}

                    <fieldset className="border border-slate-600 p-4 rounded-md">
                        <legend className="text-lg font-semibold text-sky-400 px-2">QF Parameters for Grant Streams within this Pool</legend> {/* Renamed */}
                        <div className="grid md:grid-cols-2 gap-4">
                            <Input label="Matching Multiplier/Coefficient" name="matchingMultiplier" type="number" step="0.1" value={poolData.qfParams.matchingMultiplier} onChange={handleQFParamChange} />
                            <Input label="Cap per Contributor (for QF calc)" name="capPerContributor" type="number" value={poolData.qfParams.capPerContributor} onChange={handleQFParamChange} />
                            <Input label="Max QF Match per Grant Stream" name="maxQFMatchPerGrantStream" type="number" value={poolData.qfParams.maxQFMatchPerGrantStream} onChange={handleQFParamChange} /> {/* Renamed */}
                            <Input label="Min Unique Contributors for Grant Stream Matching" name="minUniqueContributors" type="number" value={poolData.qfParams.minUniqueContributors} onChange={handleQFParamChange} /> {/* Renamed */}
                            <Input label="Min Direct Funding for Grant Stream Matching" name="minDirectFunding" type="number" value={poolData.qfParams.minDirectFunding} onChange={handleQFParamChange} /> {/* Renamed */}
                        </div>
                    </fieldset>

                    <div className="flex justify-end space-x-3 pt-4">
                        <Button type="button" variant="secondary" onClick={() => navigate('adminManagePools')}>Cancel</Button> {/* Renamed */}
                        <Button type="submit">{editMode ? 'Save Changes' : 'Create Pool'}</Button> {/* Renamed */}
                    </div>
                </form>
            </Card>
        </AdminLayout>
    );
};


const AdminSystemSettingsPage = () => {
  const { systemSettings, setSystemSettings } = useContext(AppContext);
  const [settingsData, setSettingsData] = useState(systemSettings);
  const [newAdminEmail, setNewAdminEmail] = useState('');

  const handleChange = (e) => {
    const { name, value, type } = e.target;
    setSettingsData(prev => ({ ...prev, [name]: type === 'number' ? parseFloat(value) || 0 : value }));
  };
  
  const handleAdminUserChange = (index, field, value) => {
    const updatedAdmins = [...settingsData.adminUsers];
    updatedAdmins[index][field] = value;
    setSettingsData(prev => ({ ...prev, adminUsers: updatedAdmins }));
  };

  const addAdminUser = () => {
    if (newAdminEmail.trim() && !settingsData.adminUsers.find(u => u.email === newAdminEmail)) {
        setSettingsData(prev => ({ ...prev, adminUsers: [...prev.adminUsers, {id: `admin${Date.now()}`, email: newAdminEmail}]}));
        setNewAdminEmail('');
    } else {
        alert("Please enter a valid, unique email address.");
    }
  };
  
  const removeAdminUser = (idToRemove) => {
    if (settingsData.adminUsers.length <= 1) {
        alert("Cannot remove the last admin user.");
        return;
    }
    setSettingsData(prev => ({ ...prev, adminUsers: prev.adminUsers.filter(u => u.id !== idToRemove)}));
  };


  const handleSubmit = (e) => {
    e.preventDefault();
    setSystemSettings(settingsData);
    alert('System settings updated (mock).');
  };

  return (
    <AdminLayout>
      <h2 className="text-3xl font-bold text-sky-400 mb-6">System Settings & Global Parameters</h2>
      <Card>
        <form onSubmit={handleSubmit} className="space-y-8">
          <fieldset className="border border-slate-600 p-4 rounded-md">
            <legend className="text-lg font-semibold text-sky-400 px-2">Default QF Parameters</legend>
            <Input label="Global Default QF Coefficient (for Grant Stream matching within Pools)" name="defaultQFCoefficient" type="number" step="0.1" value={settingsData.defaultQFCoefficient} onChange={handleChange} /> {/* Renamed */}
            <Input label="Platform-wide Contribution Cap for QF Calculation (if any)" name="platformContributionCap" type="number" value={settingsData.platformContributionCap} onChange={handleChange} />
          </fieldset>

          <fieldset className="border border-slate-600 p-4 rounded-md">
            <legend className="text-lg font-semibold text-sky-400 px-2">Platform Fees (Tunable)</legend>
            <Input label="Fee on Contributions to Cascades (%)" name="feeOnContributions" type="number" step="0.1" value={settingsData.feeOnContributions} onChange={handleChange} /> {/* Renamed */}
            <Input label="Fee on Matched Funds Distributed to Grant Streams (%)" name="feeOnMatchedFunds" type="number" step="0.1" value={settingsData.feeOnMatchedFunds} onChange={handleChange} /> {/* Renamed */}
          </fieldset>
          
          <fieldset className="border border-slate-600 p-4 rounded-md">
            <legend className="text-lg font-semibold text-sky-400 px-2">Admin User Management</legend>
            {settingsData.adminUsers.map((user, index) => (
                <div key={user.id} className="flex items-center space-x-2 mb-2 p-2 bg-slate-700 rounded">
                    <Input type="email" value={user.email} onChange={(e) => handleAdminUserChange(index, 'email', e.target.value)} className="flex-grow !mb-0" placeholder="Admin Email"/>
                    <Button type="button" variant="danger" onClick={() => removeAdminUser(user.id)} className="!p-1.5" title="Remove Admin"><Trash2 size={16}/></Button>
                </div>
            ))}
            <div className="flex items-end space-x-2 mt-3">
                <Input label="New Admin Email" value={newAdminEmail} onChange={(e) => setNewAdminEmail(e.target.value)} placeholder="new.admin@cascadeqf.io" className="flex-grow !mb-0"/>
                <Button type="button" onClick={addAdminUser} iconLeft={<PlusCircle size={16}/>}>Add Admin</Button>
            </div>
          </fieldset>

          <fieldset className="border border-slate-600 p-4 rounded-md">
            <legend className="text-lg font-semibold text-sky-400 px-2">Wallet Integration (Display Only)</legend>
            <p className="text-sm text-slate-300"><strong>Supported Networks:</strong> {settingsData.supportedNetworks}</p>
            <p className="text-sm text-slate-300"><strong>RPC Endpoint:</strong> {settingsData.rpcEndpoint}</p>
          </fieldset>
          
          <fieldset className="border border-slate-600 p-4 rounded-md">
            <legend className="text-lg font-semibold text-sky-400 px-2">Content Management (Mock)</legend>
            <Textarea label="Terms of Service" name="termsOfService" value={settingsData.termsOfService || "Mock Terms of Service for Cascade QF by Allo.Capital..."} onChange={handleChange} rows={3} />
            <Textarea label="Privacy Policy" name="privacyPolicy" value={settingsData.privacyPolicy || "Mock Privacy Policy for Cascade QF by Allo.Capital..."} onChange={handleChange} rows={3} />
          </fieldset>

          <div className="flex justify-end pt-4">
            <Button type="submit" iconLeft={<ShieldCheck size={18}/>}>Save System Settings</Button>
          </div>
        </form>
      </Card>
    </AdminLayout>
  );
};

export default App;
